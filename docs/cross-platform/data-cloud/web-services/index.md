---
title: Introduzione a servizi Web
description: Questa guida viene illustrato come utilizzare tecnologie del servizio web diverso. Gli argomenti trattati includono la comunicazione con i servizi REST di servizi SOAP e servizi Windows Communication Foundation.
ms.topic: article
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 48489ca7dc28dcc14a7810b15dc1ffa1fd4f7cf4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-web-services"></a>Introduzione a servizi Web

_Questa guida viene illustrato come utilizzare tecnologie del servizio web diverso. Gli argomenti trattati includono la comunicazione con i servizi REST di servizi SOAP e servizi Windows Communication Foundation._

Per funzionare correttamente, molte applicazioni per dispositivi mobili sono dipendenti nel cloud e pertanto l'integrazione di servizi web in applicazioni per dispositivi mobili è uno scenario comune. Sulla piattaforma Xamarin supporta l'utilizzo di tecnologie del servizio web diverso e include il supporto incorporato e di terze parti per l'utilizzo di servizi RESTful, ASMX e Windows Communication Foundation (WCF).

In questo articolo vengono illustrati gli argomenti seguenti:

- [Servizi REST](#rest)
- [Servizi Web ASP.Net (ASMX)](#asmx)
- [Servizi WCF](#wcf)

Per i clienti con xamarin. Forms, sono disponibili esempi completi in ognuna di queste tecnologie in utilizzando il [servizi Web di xamarin. Forms](~/xamarin-forms/data-cloud/index.md) documentazione.

> [!IMPORTANT]
> **Nota per xamarin:** In iOS 9, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.


È possibile rifiutare esplicitamente AT se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).



<a name="rest" />

## <a name="rest"></a>REST

Representational State Transfer (REST) è uno stile architetturale per la compilazione di servizi web. Richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP utilizzato dal web browser per recuperare le pagine web e per inviare dati al server. I verbi sono:

- **OTTENERE** : questa operazione viene utilizzata per recuperare i dati dal servizio web.
- **POST** : questa operazione viene utilizzata per creare un nuovo elemento di dati nel servizio web.
- **INSERIRE** : questa operazione viene utilizzata per aggiornare un elemento di dati nel servizio web.
- **PATCH** : questa operazione viene utilizzata per aggiornare un elemento di dati nel servizio web che descrive un set di istruzioni su come l'elemento deve essere modificato. Nell'applicazione di esempio non viene utilizzato questo verbo.
- **ELIMINARE** : questa operazione viene utilizzata per eliminare un elemento di dati nel servizio web.

API conformi al resto del servizio Web vengono chiamati RESTful API e vengono definiti utilizzando:

- Un URI di base.
- Metodi HTTP, ad esempio GET, POST, PUT, PATCH o DELETE.
- Un tipo di supporto per i dati, ad esempio JavaScript Object Notation (JSON).

La semplicità di resto ha contribuito a renderlo il metodo principale per l'accesso ai servizi web in applicazioni per dispositivi mobili.

## <a name="consuming-rest-services"></a>Utilizzo di servizi REST

Esistono una serie di librerie e le classi che consentono di utilizzare i servizi REST e discuterne le sottosezioni seguenti. Per ulteriori informazioni sull'utilizzo di un servizio REST, vedere [utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md).

### <a name="httpclient"></a>HttpClient

Il [Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http) fornisce il `HttpClient` (classe), viene utilizzato per inviare e ricevere richieste su HTTP. Fornisce funzionalità per l'invio delle richieste HTTP e la ricezione delle risposte HTTP da una risorsa identificata URI. Ogni richiesta viene inviata come operazione asincrona. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto di Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto dal servizio web dopo aver effettuata una richiesta HTTP. Contiene informazioni sulla risposta, incluso il codice di stato, intestazioni e corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi del `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, in base al formato dei dati.

Per ulteriori informazioni sul `HttpClient` classe, vedere [la creazione dell'oggetto HTTPClient](~/xamarin-forms/data-cloud/consuming/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Chiamate ai servizi web con `HTTPWebRequest` comporta:

-  Creazione dell'istanza di richiesta per un particolare URI.
-  Impostazione di varie proprietà HTTP nell'istanza di richiesta.
-  Recupero di un `HttpWebResponse` dalla richiesta.
-  Lettura di dati all'esterno della risposta.

Ad esempio, il codice seguente recupera i dati da U.S. Servizio web di libreria di medicina nazionale:

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

Nell'esempio precedente viene creato un `HttpWebRequest` che restituisce i dati formattati come JSON. I dati vengono restituiti un `HttpWebResponse`, da cui un `StreamReader` può essere ottenuto per leggere i dati.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Utilizza un altro approccio per l'utilizzo di servizi REST di [RestSharp](http://restsharp.org/) libreria. RestSharp incapsula le richieste HTTP, incluso il supporto per il recupero dei risultati come contenuto della stringa non elaborato o come un oggetto in c#. Ad esempio, il codice seguente effettua una richiesta per Stati Uniti Servizio web di libreria di medicina nazionali e recupera i risultati come JSON stringa formattata:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` è un metodo che eseguirà la stringa JSON non elaborata dal `RestSharp.RestResponse.Content` proprietà e convertirlo in un oggetto in c#. La deserializzazione dei dati restituiti dai servizi web è descritto più avanti in questo articolo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Oltre alle classi disponibili nella base Mono libreria di classi di base (BCL), ad esempio `HttpWebRequest`e le librerie di terze parti in c#, ad esempio RestSharp, classi specifiche della piattaforma sono disponibili anche per l'utilizzo di servizi web. Ad esempio, in iOS, il `NSUrlConnection` e `NSMutableUrlRequest` classi possono essere utilizzate.

Esempio di codice seguente viene illustrato come chiamare negli Stati Uniti Servizio web National di medicina della libreria utilizzando le classi di iOS:

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

In genere, le classi specifiche della piattaforma per l'utilizzo di servizi web devono essere limitate agli scenari in cui viene trasferito in c# il codice nativo. Dove possibile, codice di accesso del servizio web devono essere portabili in modo che possano essere condivisione tra piattaforme.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

È anche possibile chiamare i servizi web di [servizio Stack](http://www.servicestack.net/) libreria. Ad esempio, il codice seguente viene illustrato come utilizzare servizio Stack `IServiceClient.GetAsync` metodo per rilasciare una richiesta di servizio:

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
> **Nota:** mentre strumenti quali ServiceStack e RestSharp semplificano la chiamata e utilizzare servizi di REST, è talvolta non semplice per utilizzare XML o JSON che non è conforme allo standard _DataContract_ serializzazione convenzioni. Se necessario, attivare la richiesta e di gestire la serializzazione appropriata in modo esplicito utilizzando la libreria ServiceStack.Text descritta di seguito.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Utilizzo di dati REST

I servizi web rESTful utilizzano in genere i messaggi JSON per restituire dati al client. JSON è basata su testo, di interscambio dei dati formato che produce compact payload, che comporta i requisiti di larghezza di banda ridotta per l'invio di dati. In questa sezione verranno esaminati i meccanismi per l'utilizzo di risposte REST in JSON e normale-vecchio-XML (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Sulla piattaforma Xamarin viene fornito con il supporto per JSON predefinita. Utilizzando un `JsonObject`, risultati possono essere recuperati come illustrato nell'esempio di codice seguente:

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

Tuttavia, è importante tenere presente che il `System.Json` strumenti caricano l'intera dei dati in memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

Il [NewtonSoft JSON.NET libreria](http://www.newtonsoft.com/json) è una libreria ampiamente usata per serializzare e deserializzare i messaggi JSON. Esempio di codice seguente viene illustrato come utilizzare JSON.NET per deserializzare un messaggio JSON in un oggetto in c#:

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

ServiceStack.Text è progettata per funzionare con la libreria ServiceStack una libreria di serializzazione JSON. Esempio di codice seguente viene illustrato come analizzare JSON usando un `ServiceStack.Text.JsonObject`:

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

In caso di utilizzo di un servizio web REST basato su XML, è possibile usare LINQ to XML per analizzare il codice XML e popolare un c# inline di oggetto, come illustrato nell'esempio di codice seguente:

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

ASMX offre la possibilità di compilare servizi web che inviano messaggi utilizzando l'oggetto accesso protocollo SOAP (Simple). SOAP è un protocollo indipendente dalla piattaforma e indipendente dal linguaggio per la compilazione e l'accesso ai servizi web. I consumer di un servizio ASMX non è necessario conoscere la piattaforma, un modello a oggetti o un linguaggio di programmazione utilizzato per implementare il servizio. È sufficiente comprendere come inviare e ricevere messaggi SOAP.

Un messaggio SOAP è un documento XML contenente gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come un messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio dati di autenticazione. Se il *intestazione* elemento è presente deve essere il primo elemento figlio del *busta* elemento.
- Richiesta *corpo* elemento che contiene il messaggio SOAP per il destinatario.
- Facoltativo *errore* elemento che viene utilizzato per indicare i messaggi di errore. Se il *errore* elemento è presente, deve essere un elemento figlio del *corpo* elemento.

SOAP possono operare su numerosi protocolli di trasporto, come HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. Sulla piattaforma Xamarin supporta implementazioni di SOAP 1.1 standard su HTTP, e questo include il supporto per molte delle configurazioni del servizio ASMX standard.

### <a name="generating-a-proxy"></a>Generazione di un Proxy

Oggetto *proxy* deve essere generato per l'utilizzo di un servizio ASMX, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito mediante dispendiosa in termini di metadati del servizio che definisce i metodi e la configurazione del servizio associato. I metadati vengono esposti come un documento Web Services Description Language (WSDL) che viene generato dal servizio web. Il proxy è compilato con Visual Studio per Mac o Visual Studio per aggiungere un riferimento web per il servizio web per i progetti specifici della piattaforma.

L'URL del servizio web può essere un'origine remota ospitato o risorsa di sistema di file locale accessibile tramite il `file:///` prefisso di percorso, ad esempio:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "L'URL del servizio web può essere un'origine remota ospitato o risorse di sistema di file locale accessibili tramite il prefisso di percorso di file")](images/add-webreference-dialog.png#lightbox)

Il proxy verrà generato nella cartella del progetto Web o i riferimenti al servizio. Poiché viene generato un proxy di codice, non deve essere modificato.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Aggiunta manuale di un Proxy a un progetto

Se si dispone di un proxy esistente che è stato generato utilizzando gli strumenti compatibili, questo output può essere utilizzato quando è incluso come parte del progetto. In Visual Studio per Mac, usare il **aggiungere i file...** opzione di menu per aggiungere il proxy. Inoltre, è necessario *System.Web.Services.dll* per fare riferimento in modo esplicito mediante il **aggiunta di riferimenti...** finestra di dialogo.

### <a name="consuming-the-proxy"></a>Utilizzo del Proxy

Le classi proxy generato forniscono metodi per l'utilizzo del servizio web che utilizzano il modello di progettazione del modello di programmazione asincrono (APM). In questo modello di un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il *BeginOperationName* metodo inizia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo la chiamata *BeginOperationName*, un'applicazione può continuare l'esecuzione di istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve chiamare anche *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo del servizio web sincrono. Esempio di codice seguente viene illustrato un esempio:

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

Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nello stesso `Task` oggetto. Fornito da più overload di questo incapsulamento di `Task.Factory.FromAsync` metodo. Questo metodo crea un `Task` che esegue il `TodoService.EndGetTodoItems` metodo una volta il `TodoService.BeginGetTodoItems` metodo viene completato, con il `null` che indica che nessun dato è stato passato nel parametro di `BeginGetTodoItems` delegato. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Per ulteriori informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/en-us/library/ms228963(v=vs.110).aspx) e [TPL e .NET Framework programmazione asincrona tradizionale](https://msdn.microsoft.com/en-us/library/dd997423(v=vs.110).aspx) su MSDN.

Per ulteriori informazioni sull'utilizzo di un servizio ASMX, vedere [utilizzo di un servizio Web di ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF è framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite protette, affidabile, transazioni e interoperative.

WCF viene descritto un servizio con un'ampia gamma di diversi contratti che includono i seguenti:

- **Contratti dati** : definire le strutture di dati che costituiscono la base per il contenuto all'interno di un messaggio.
- **Contratti di messaggio** : scrivere messaggi dai contratti dati esistenti.
- **Contratti di errore** : consente di specificare gli errori personalizzati di SOAP.
- **Contratti di servizio** : specificare le operazioni che supportano servizi e i messaggi necessari per l'interazione con ogni operazione. Specificano inoltre eventuali comportamenti di errore personalizzato che possono essere associato a operazioni in ogni servizio.

Esistono differenze tra i servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità che fornisce ASMX: messaggi SOAP su HTTP.

In generale, sulla piattaforma Xamarin supporta lo stesso subset di sul lato client di WCF che viene fornito con il runtime Silverlight. Ciò include le implementazioni più comuni di codifica e protocollo di WCF, ovvero codificata in formato testo di messaggi SOAP su HTTP tramite protocollo di trasporto di `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso di strumenti disponibili solo in un ambiente di Windows per generare il proxy.

Per ulteriori informazioni sull'utilizzo sulla piattaforma Xamarin utilizzare WCF web servizio con il `BasicHttpBinding` classe, vedere [procedura dettagliata: utilizzo di WCF](walkthrough-working-with-wcf.md).

### <a name="generating-a-proxy"></a>Generazione di un Proxy

Oggetto *proxy* deve essere generato per utilizzare un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito mediante dispendiosa in termini di metadati del servizio che definiscono i metodi e la configurazione del servizio associato. I metadati vengono esposti nel formato di un documento Web Services Description Language (WSDL) che viene generato dal servizio web. Il proxy può essere compilato utilizzando il Provider di riferimento Microsoft servizi Web WCF in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio web a una libreria Standard di .NET.

Un'alternativa alla creazione del proxy utilizzando Provider riferimento del servizio Web di Microsoft WCF in Visual Studio 2017 consiste nell'utilizzare lo strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per ulteriori informazioni, vedere [strumento ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>La configurazione del Proxy

La configurazione del proxy generato avrà in genere due argomenti di configurazione (a seconda di SOAP 1.1/ASMX o WCF) durante l'inizializzazione: il `EndpointAddress` e/o le informazioni di associazione, come illustrato nell'esempio riportato di seguito:

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

Un'associazione viene utilizzata per specificare il trasporto, codifica e protocollo dettagli necessari per le applicazioni e servizi comunicare tra loro. Il `BasicHttpBinding` specifica che i messaggi SOAP con codifica testo verranno inviati tramite il protocollo di trasporto HTTP. Specifica un indirizzo endpoint consente all'applicazione per la connessione tra le diverse istanze del servizio WCF, purché non ci siano più istanze pubblicate.

### <a name="consuming-the-proxy"></a>Utilizzo del Proxy

Le classi proxy generato forniscono metodi per l'utilizzo dei servizi web che utilizzano il modello di progettazione del modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il *BeginOperationName* metodo inizia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo la chiamata *BeginOperationName*, un'applicazione può continuare l'esecuzione di istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve chiamare anche *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo del servizio web sincrono. Esempio di codice seguente viene illustrato un esempio:

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

Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nello stesso `Task` oggetto. Fornito da più overload di questo incapsulamento di `Task.Factory.FromAsync` metodo. Questo metodo crea un `Task` che esegue il `TodoServiceClient.EndGetTodoItems` metodo una volta il `TodoServiceClient.BeginGetTodoItems` metodo viene completato, con il `null` che indica che nessun dato è stato passato nel parametro di `BeginGetTodoItems` delegato. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Per ulteriori informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/en-us/library/ms228963(v=vs.110).aspx) e [TPL e .NET Framework programmazione asincrona tradizionale](https://msdn.microsoft.com/en-us/library/dd997423(v=vs.110).aspx) su MSDN.

Per ulteriori informazioni sull'utilizzo di un servizio WCF, vedere [utilizzo di un servizio Web di Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Utilizzando la sicurezza del trasporto

Servizi WCF possono utilizzare la protezione del trasporto per impedire l'intercettazione di messaggi. La piattaforma Xamarin supporta le associazioni che si avvalgono di sicurezza a livello di trasporto mediante SSL. Tuttavia, potrebbe essere casi in cui lo stack potrebbe essere necessario convalidare il certificato, determinando un comportamento imprevisto. La convalida può essere sottoposto a override registrando un `ServerCertificateValidationCallback` delegato prima di richiamare il servizio, come illustrato nell'esempio di codice seguente:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

In questo modo la crittografia di trasporto, ignorando la convalida del certificato sul lato server. Tuttavia, questo approccio in modo efficace ignorerà i problemi di attendibilità associati al certificato e potrebbe non essere appropriato. Per ulteriori informazioni, vedere [tramite Trusted osservanza radici](http://www.mono-project.com/UsingTrustedRootsRespectfully) su [mono project.com](http://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Utilizzo della protezione delle credenziali Client

Servizi WCF possono inoltre richiedere i client del servizio per l'autenticazione utilizzando le credenziali. Sulla piattaforma Xamarin non supporta il protocollo WS-Security, che consente ai client di inviare le credenziali all'interno della busta del messaggio SOAP. Tuttavia, sulla piattaforma Xamarin supporta la possibilità di inviare le credenziali di autenticazione di base HTTP al server specificando appropriata `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Quindi, è possibile specificare le credenziali di autenticazione di base:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Nell'esempio precedente, se viene visualizzato il messaggio "Esaurito trampolines di tipo 0" è possibile aumentare il numero di trampolines tipo 0 aggiungendo il `–aot “trampolines={number of trampolines}”` argomento per la compilazione. Per ulteriori informazioni, vedere [risoluzione dei problemi](~/ios/troubleshooting/troubleshooting.md#trampolines).

Per ulteriori informazioni sull'autenticazione di base HTTP, anche se nel contesto di un servizio web REST, vedere [l'autenticazione di un servizio Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="summary"></a>Riepilogo

Questa guida viene illustrato come utilizzare tecnologie del servizio web diverso. Gli argomenti trattati includono la comunicazione con i servizi REST di servizi SOAP e servizi Windows Communication Foundation.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di servizi Web](https://developer.xamarin.com/samples/mobile/WebServices/WebServiceSamples/)
- [Servizi Web di xamarin. Forms](~/xamarin-forms/data-cloud/index.md)
- [ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](http://msdn.microsoft.com/en-us/library/system.servicemodel.basichttpbinding.aspx)
