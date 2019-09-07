---
title: Introduzione ai servizi Web
description: In questa guida viene illustrato come utilizzare tecnologie di servizi Web diverse. Gli argomenti trattati includono la comunicazione con i servizi REST, i servizi SOAP e i servizi Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: f914a158135d34b59fa3d1b95972c988a44dd36b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765978"
---
# <a name="introduction-to-web-services"></a>Introduzione ai servizi Web

_In questa guida viene illustrato come utilizzare tecnologie di servizi Web diverse. Gli argomenti trattati includono la comunicazione con i servizi REST, i servizi SOAP e i servizi Windows Communication Foundation._

Per funzionare correttamente, molte applicazioni per dispositivi mobili dipendono dal cloud e pertanto l'integrazione dei servizi Web nelle applicazioni per dispositivi mobili è uno scenario comune. La piattaforma Novell supporta l'utilizzo di diverse tecnologie di servizi Web e include supporto integrato e di terze parti per l'utilizzo di servizi RESTful, ASMX e Windows Communication Foundation (WCF).

Per i clienti che usano Novell. Forms, sono disponibili esempi completi che usano ciascuna di queste tecnologie nella documentazione relativa ai [servizi Web Novell. Forms](~/xamarin-forms/data-cloud/index.yml) .

> [!IMPORTANT]
> In iOS 9, la sicurezza del trasporto app (ATS) applica connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo in tal modo la divulgazione accidentale di informazioni riservate.
> Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

È possibile rifiutare esplicitamente ATS se non è possibile usare il protocollo e la `HTTPS` comunicazione protetta per le risorse Internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

Representational State Transfer (REST) è uno stile architetturale per la creazione di servizi web. Richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP che utilizzano i web browser per recuperare le pagine web e per inviare dati ai server. I verbi sono:

- **OTTENERE** : questa operazione viene usata per recuperare i dati dal servizio web.
- **POST** : questa operazione viene usata per creare un nuovo elemento di dati nel servizio web.
- **INSERIRE** : questa operazione viene usata per aggiornare un elemento di dati nel servizio web.
- **PATCH** : questa operazione viene usata per aggiornare un elemento di dati nel servizio web mediante la descrizione di un set di istruzioni sul modo in cui l'elemento deve essere modificato. Questo verbo non viene utilizzato nell'applicazione di esempio.
- **Elimina** : questa operazione viene usata per eliminare un elemento di dati nel servizio web.

Le API che rispettano REST del servizio Web vengono chiamate le API RESTful e vengono definiti utilizzando:

- Un URI di base.
- Metodi HTTP, ad esempio GET, POST, PUT, PATCH o DELETE.
- Un tipo di supporto per i dati, ad esempio JavaScript Object Notation (JSON).

La semplicità di REST ci ha aiutati a renderlo il principale metodo per l'accesso ai servizi web nelle applicazioni per dispositivi mobili.

## <a name="consuming-rest-services"></a>Utilizzo dei servizi REST

Sono disponibili numerose librerie e classi che possono essere utilizzate per utilizzare i servizi REST e le sottosezioni seguenti. Per ulteriori informazioni sull'utilizzo di un servizio REST, vedere [utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

Le [librerie client HTTP Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http) forniscono la `HttpClient` classe, utilizzata per inviare e ricevere richieste tramite http. Fornisce funzionalità per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata dall'URI. Ogni richiesta viene inviata come operazione asincrona. Per altre informazioni sulle operazioni asincrone, vedere [Panoramica del supporto asincrono](~/cross-platform/platform/async.md).

Il `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto dal servizio web dopo aver effettuata una richiesta HTTP. Contiene informazioni sulla risposta, tra cui il codice di stato, le intestazioni e il corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi dei `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, in base al formato dei dati.

Per ulteriori informazioni sulla `HttpClient` classe, vedere [creazione dell'oggetto HTTPClient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

La chiamata ai servizi `HTTPWebRequest` Web con comporta:

- Creazione dell'istanza della richiesta per un URI specifico.
- Impostazione di varie proprietà HTTP nell'istanza della richiesta.
- Recupero di un `HttpWebResponse` oggetto dalla richiesta.
- Lettura dei dati dalla risposta.

Il codice seguente, ad esempio, consente di recuperare i dati dagli Stati Uniti National Library of Medicine Web Service:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
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

Nell'esempio precedente viene creato `HttpWebRequest` un oggetto che restituirà i dati formattati come JSON. I dati vengono restituiti in un `HttpWebResponse`oggetto, da cui `StreamReader` è possibile ottenere un oggetto per leggere i dati.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Un altro approccio all'utilizzo dei servizi REST consiste nell'utilizzare la libreria [RestSharp](http://restsharp.org/) . RestSharp incapsula le richieste HTTP, incluso il supporto per il recupero dei risultati come contenuto della stringa non elaborata o come C# oggetto deserializzato. Il codice seguente, ad esempio, effettua una richiesta al National Library of Medicine Web Service e recupera i risultati sotto forma di stringa in formato JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm`è un metodo che prenderà la stringa JSON non elaborata `RestSharp.RestResponse.Content` dalla proprietà e la convertirà C# in un oggetto. La deserializzazione dei dati restituiti dai servizi Web viene discussa più avanti in questo articolo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Oltre alle classi disponibili nella libreria di classi base mono (BCL), ad esempio `HttpWebRequest`, e le librerie di terze parti C# , ad esempio RestSharp, sono disponibili anche classi specifiche della piattaforma per l'utilizzo di servizi Web. In iOS, ad esempio, è `NSUrlConnection` possibile `NSMutableUrlRequest` usare le classi e.

Nell'esempio di codice seguente viene illustrato come chiamare il metodo US Libreria nazionale di servizi Web di medicina con le classi iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
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

Un'altra opzione per chiamare i servizi Web è la libreria [dello stack dei servizi](http://www.servicestack.net/) . Ad esempio, nel codice seguente viene illustrato come utilizzare il `IServiceClient.GetAsync` metodo dello stack di servizi per emettere una richiesta di servizio:

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

Servizi web rESTful utilizzano in genere i messaggi JSON per restituire dati al client. JSON è un formato di interscambio dei dati basato su testo che genera payload Compact, con conseguente riduzione dei requisiti di larghezza di banda quando si inviano dati. In questa sezione vengono esaminati i meccanismi per l'utilizzo delle risposte RESTful in JSON e di POX (Plain-Old-XML).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

La piattaforma Novell viene fornita con il supporto per JSON predefinito. Usando un `JsonObject`, i risultati possono essere recuperati come illustrato nell'esempio di codice seguente:

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

Tuttavia, è importante tenere presente che `System.Json` gli strumenti di caricano interamente i dati in memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

La [libreria JSON.NET di NewtonSoft](http://www.newtonsoft.com/json) è una libreria ampiamente utilizzata per la serializzazione e la deserializzazione dei messaggi JSON. L'esempio di codice seguente illustra come usare JSON.NET per deserializzare un messaggio JSON in un C# oggetto:

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

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack. Text è una libreria di serializzazione JSON progettata per funzionare con la libreria ServiceStack. Nell'esempio di codice seguente viene illustrato come analizzare JSON utilizzando `ServiceStack.Text.JsonObject`:

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

ASMX consente di compilare servizi Web che inviano messaggi utilizzando il Simple Object Access Protocol (SOAP). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP.

Un messaggio SOAP è un documento XML che contiene gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se il *intestazione* è presente l'elemento deve essere il primo elemento figlio delle *busta* elemento.
- Un necessaria *corpo* elemento che contiene il messaggio SOAP deve essere il destinatario.
- Facoltativo *Fault* elemento che viene usato per indicare i messaggi di errore. Se il *Fault* l'elemento è presente, deve essere un elemento figlio delle *corpo* elemento.

SOAP possono agire su molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. La piattaforma Xamarin supporta implementazioni SOAP 1.1 standard su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

### <a name="generating-a-proxy"></a>Generazione di un proxy

È necessario generare un *proxy* per utilizzare un servizio ASMX, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definisce i metodi e la configurazione del servizio associato. Questi metadati vengono esposti come documento di Web Services Description Language (WSDL) generato dal servizio Web. Il proxy viene compilato usando Visual Studio per Mac o Visual Studio per aggiungere un riferimento Web per il servizio Web ai progetti specifici della piattaforma.

L'URL del servizio Web può essere un'origine remota ospitata o una risorsa file system locale accessibile `file:///` tramite il prefisso del percorso, ad esempio:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "L'URL del servizio Web può essere un'origine remota ospitata o una risorsa di file system locale accessibile tramite il prefisso del percorso del file")](images/add-webreference-dialog.png#lightbox)

Viene generato il proxy nella cartella riferimenti Web o del servizio del progetto. Poiché il codice generato da un proxy non deve essere modificato.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Aggiunta manuale di un proxy a un progetto

Se si dispone di un proxy esistente che è stato generato con strumenti compatibili, questo output può essere utilizzato quando è incluso nel progetto. In Visual Studio per Mac usare l' **aggiunta di file..** . opzione di menu per aggiungere il proxy. Inoltre, è necessario fare riferimento in modo esplicito a *System. Web. Services. dll* utilizzando l' **aggiunta di riferimenti...** dialogo.

### <a name="consuming-the-proxy"></a>Utilizzo del proxy

Le classi proxy generate forniscono metodi per l'utilizzo del servizio web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello di un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Il codice seguente ne è un esempio:

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

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `Task.Factory.FromAsync` (metodo). Questo metodo crea un `Task` oggetto che esegue il `TodoService.EndGetTodoItems` metodo una volta `TodoService.BeginGetTodoItems` completato il metodo, con il `null` parametro che indica che non è stato passato `BeginGetTodoItems` alcun dato al delegato. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Per ulteriori informazioni su APM, vedere il [modello di programmazione asincrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e la tradizionale .NET Framework la programmazione asincrona](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

Per ulteriori informazioni sull'utilizzo di un servizio ASMX, vedere la pagina relativa all'utilizzo di [un servizio Web ASP.NET (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF è il Framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite sicure, affidabili, transazionali e interoperabile.

WCF descrive un servizio con un'ampia gamma di contratti diversi che includono i seguenti:

- **I contratti dati** : definire le strutture di dati che costituiscono la base per il contenuto all'interno di un messaggio.
- **Contratti di messaggio** : comporre i messaggi da contratti dati esistenti.
- **I contratti di errore** -Consenti errori SOAP personalizzati essere specificato.
- **I contratti di servizio** : specificare le operazioni che supportano servizi e i messaggi necessari per l'interazione con ogni operazione. Specificano anche eventuali comportamenti di errore personalizzato che possono essere associato a operazioni su ogni servizio.

Esistono differenze tra servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità offerte da ASMX: messaggi SOAP su HTTP.

> [!IMPORTANT]
> Il supporto della piattaforma Novell per WCF è limitato ai messaggi SOAP con codifica testo tramite HTTP/HTTPS mediante la `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso degli strumenti disponibili solo in un ambiente di Windows per generare il proxy.

### <a name="generating-a-proxy"></a>Generazione di un proxy

Oggetto *proxy* deve essere generato per l'utilizzo di un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definiscono i metodi e la configurazione del servizio associato. Questi metadati viene esposta sotto forma di un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy può essere compilato usando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio Web a una libreria di .NET Standard.

Un'alternativa alla creazione del proxy utilizzando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 è usare la strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per altre informazioni, vedere [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configurazione del proxy

La configurazione del proxy generato in genere accetta due argomenti di configurazione (a seconda di SOAP 1.1/asmx o WCF) durante l' `EndpointAddress` inizializzazione: e/o le informazioni di binding associate, come illustrato nell'esempio seguente:

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

Viene utilizzata un'associazione per specificare il trasporto, codifica e i dettagli di protocollo necessari per le applicazioni e servizi comunicare tra loro. Il `BasicHttpBinding` specifica che verranno inviati i messaggi SOAP con codifica del testo tramite il protocollo di trasporto HTTP. Specifica un indirizzo endpoint consente all'applicazione di connettersi alle diverse istanze del servizio WCF, condizione che sono presenti più istanze pubblicate.

### <a name="consuming-the-proxy"></a>Utilizzo del proxy

Le classi proxy generate forniscono metodi per l'uso dei servizi web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Il codice seguente ne è un esempio:

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

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `Task.Factory.FromAsync` (metodo). Questo metodo crea un `Task` oggetto che esegue il `TodoServiceClient.EndGetTodoItems` metodo una volta `TodoServiceClient.BeginGetTodoItems` completato il metodo, con il `null` parametro che indica che non è stato passato `BeginGetTodoItems` alcun dato al delegato. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Per ulteriori informazioni su APM, vedere il [modello di programmazione asincrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e la tradizionale .NET Framework la programmazione asincrona](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

Per ulteriori informazioni sull'utilizzo di un servizio WCF, vedere [utilizzare un servizio Web Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Uso della sicurezza del trasporto

I servizi WCF possono utilizzare la sicurezza a livello di trasporto per impedire l'intercettazione di messaggi. La piattaforma Novell supporta le associazioni che utilizzano la sicurezza a livello di trasporto tramite SSL. In alcuni casi, tuttavia, potrebbe essere necessario che lo stack convalidi il certificato, il che comporta un comportamento imprevisto. La convalida può essere sottoposta a override registrando un `ServerCertificateValidationCallback` delegato prima di richiamare il servizio, come illustrato nell'esempio di codice seguente:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Questa operazione mantiene la crittografia del trasporto ignorando la convalida del certificato sul lato server. Questo approccio, tuttavia, ignora in modo efficace i problemi di attendibilità associati al certificato e potrebbe non essere appropriato. Per altre informazioni, vedere [uso di radici attendibili rispetto](https://www.mono-project.com/UsingTrustedRootsRespectfully) a [Mono-Project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Uso della sicurezza delle credenziali client

I servizi WCF possono anche richiedere ai client del servizio di eseguire l'autenticazione utilizzando le credenziali. La piattaforma Novell non supporta il protocollo WS-Security, che consente ai client di inviare le credenziali all'interno della busta dei messaggi SOAP. Tuttavia, la piattaforma Novell supporta la possibilità di inviare le credenziali di autenticazione di base HTTP al server specificando l' `ClientCredentialType`oggetto appropriato:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

È quindi possibile specificare le credenziali di autenticazione di base:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Nell'esempio precedente, se si riceve il messaggio "esaurito i trampolini di tipo 0", è possibile aumentare il numero di elastici di tipo 0 aggiungendo l' `–aot “trampolines={number of trampolines}”` argomento alla compilazione. Per altre informazioni, vedere [Risoluzione dei problemi](~/ios/troubleshooting/troubleshooting.md#trampolines).

Per ulteriori informazioni sull'autenticazione di base HTTP, anche se nel contesto di un servizio Web REST, vedere [autenticazione di un servizio Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Collegamenti correlati

- [Servizi Web in Novell. Forms](~/xamarin-forms/data-cloud/index.yml)
- [ServiceModel Metadata Utility Tool (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
