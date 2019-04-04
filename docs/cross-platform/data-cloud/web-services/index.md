---
title: Introduzione a servizi Web
description: Questa guida viene illustrato come utilizzare tecnologie del servizio web diverso. Gli argomenti trattati includono la comunicazione con i servizi REST, SOAP servizi e dei servizi Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: afebe7f491855844e18bf054d665cf8d54e8f353
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672391"
---
# <a name="introduction-to-web-services"></a>Introduzione a servizi Web

_Questa guida viene illustrato come utilizzare tecnologie del servizio web diverso. Gli argomenti trattati includono la comunicazione con i servizi REST, SOAP servizi e dei servizi Windows Communication Foundation._

Per funzionare correttamente, molte applicazioni per dispositivi mobili sono dipendente nel cloud e pertanto l'integrazione di servizi web nelle applicazioni per dispositivi mobili è uno scenario comune. La piattaforma Xamarin supporta il consumo di tecnologie del servizio web diverso e include il supporto incorporato e di terze parti per l'uso dei servizi RESTful, ASMX e di Windows Communication Foundation (WCF).

Per i clienti che usano xamarin. Forms, sono disponibili esempi completi che usano ognuna di queste tecnologie nel [servizi Web di xamarin. Forms](~/xamarin-forms/data-cloud/index.md) documentazione.

> [!IMPORTANT]
> In iOS 9, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate.
> Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

È possibile rifiutare esplicitamente di ATS se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

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

## <a name="consuming-rest-services"></a>Utilizzo di servizi REST

Esistono una serie di librerie e le classi che consente di utilizzare i servizi REST e discuterne le sottosezioni seguenti. Per altre informazioni sull'utilizzo di un servizio REST, vedere [utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md).

### <a name="httpclient"></a>HttpClient

Il [Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http) fornisce il `HttpClient` (classe), che consente di inviare e ricevere richieste tramite HTTP. Fornisce funzionalità per l'invio delle richieste HTTP e la ricezione delle risposte HTTP da una risorsa identificata dall'URI. Ogni richiesta viene inviata come operazione asincrona. Per altre informazioni sulle operazioni asincrone, vedere [Panoramica del supporto asincrono](~/cross-platform/platform/async.md).

Il `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto dal servizio web dopo aver effettuata una richiesta HTTP. Contiene informazioni relative alla risposta, incluso il codice di stato, intestazioni e corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi dei `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, in base al formato dei dati.

Per altre informazioni sul `HttpClient` classe, vedere [creazione dell'oggetto HTTPClient](~/xamarin-forms/data-cloud/consuming/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Chiamata dei servizi web con `HTTPWebRequest` comporta:

-  Creazione dell'istanza di richiesta per un determinato URI.
-  Impostazione di diverse proprietà HTTP nell'istanza di richiesta.
-  Recupero di un `HttpWebResponse` dalla richiesta.
-  Durante la lettura dei dati dalla risposta.

Ad esempio, il codice seguente recupera i dati da Stati Uniti. Servizio web della libreria di medicina nazionale:

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

Nell'esempio precedente viene creato un `HttpWebRequest` che restituisce i dati formattati come JSON. I dati vengono restituiti un' `HttpWebResponse`, da cui un `StreamReader` può essere ottenuto per leggere i dati.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Un altro approccio per l'utilizzo di servizi REST Usa la [RestSharp](http://restsharp.org/) libreria. RestSharp incapsula le richieste HTTP, incluso il supporto per il recupero dei risultati come contenuto della stringa non elaborata o come un deserializzati C# oggetto. Ad esempio, il codice seguente effettua una richiesta agli Stati Uniti Stringa formattata nazionali servizio web della libreria di medicina e recupera i risultati in un formato JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` è un metodo che verrà eseguita la stringa JSON non elaborata dal `RestSharp.RestResponse.Content` proprietà e convertirlo in un C# oggetto. Più avanti in questo articolo viene illustrata la deserializzazione dei dati restituiti dai servizi web.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Oltre alle classi disponibili nella base Mono libreria di classi (BCL), ad esempio `HttpWebRequest`e di terze parti C# librerie, ad esempio RestSharp, classi specifiche della piattaforma sono disponibili anche per l'uso dei servizi web. Ad esempio, in iOS, il `NSUrlConnection` e `NSMutableUrlRequest` classi possono essere utilizzate.

Esempio di codice seguente viene illustrato come chiamare negli Stati Uniti Servizio web nazionale della libreria di medicina utilizzando le classi di iOS:

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

In generale, le classi specifiche della piattaforma per l'uso dei servizi web devono essere limitate agli scenari in cui viene fatta migrare il codice nativo su C#. Dove possibile, codice di accesso ai servizi web devono essere portabili in modo che può essere multipiattaforma condiviso.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Un'altra opzione per la chiamata dei servizi web è il [Stack servizio](http://www.servicestack.net/) libreria. Ad esempio, il codice seguente viene illustrato come usare lo Stack servizio `IServiceClient.GetAsync` metodo per inviare una richiesta di servizio:

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
> Mentre strumenti quali ServiceStack e RestSharp semplificano chiamare e utilizzare posiziona i servizi, è talvolta non semplice utilizzare XML o JSON che non è conforme allo standard _DataContract_ convenzioni di serializzazione. Se necessario, richiamare la richiesta e di gestire la serializzazione appropriata in modo esplicito usando la libreria ServiceStack.Text illustrata di seguito.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Utilizzo dei dati RESTful

Servizi web rESTful utilizzano in genere i messaggi JSON per restituire dati al client. JSON è un basata su testo, i dati formato di interscambio che produce compattare i payload, che comporta i requisiti di larghezza di banda ridotta per l'invio di dati. In questa sezione verranno esaminati meccanismi per l'utilizzo di risposte RESTful in JSON e Plain-Old-XML (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

La piattaforma Xamarin viene fornito con supporto per JSON per impostazione predefinita. Usando un `JsonObject`, i risultati possono essere recuperati come illustrato nell'esempio di codice seguente:

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

Tuttavia, è importante tenere presente che il `System.Json` strumenti caricare l'intero dei dati in memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

Il [libreria NewtonSoft JSON.NET](http://www.newtonsoft.com/json) è una libreria ampiamente usata per serializzare e deserializzare i messaggi JSON. Esempio di codice seguente viene illustrato come utilizzare JSON.NET per deserializzare un messaggio JSON in un C# oggetto:

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

In caso di utilizzo di un servizio web REST basato su XML, LINQ to XML può essere utilizzato per analizzare il codice XML e popolare un C# dell'oggetto inline, come illustrato nell'esempio di codice seguente:

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

ASMX offre la possibilità di compilare servizi web che inviano messaggi usando la SOAP Simple Object Access Protocol (). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP.

Un messaggio SOAP è un documento XML che contiene gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se il *intestazione* è presente l'elemento deve essere il primo elemento figlio delle *busta* elemento.
- Un necessaria *corpo* elemento che contiene il messaggio SOAP deve essere il destinatario.
- Facoltativo *Fault* elemento che viene usato per indicare i messaggi di errore. Se il *Fault* l'elemento è presente, deve essere un elemento figlio delle *corpo* elemento.

SOAP possono agire su molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. La piattaforma Xamarin supporta implementazioni SOAP 1.1 standard su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

### <a name="generating-a-proxy"></a>Generazione di un Proxy

Oggetto *proxy* deve essere generato per l'utilizzo di un servizio ASMX, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definisce i metodi e la configurazione del servizio associato. Questi metadati vengono esposti come un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy viene compilato con Visual Studio per Mac o Visual Studio per aggiungere un riferimento web per il servizio web per i progetti specifici della piattaforma.

L'URL del servizio web può essere un'origine remota ospitato o risorsa di sistema di file locale accessibile tramite il `file:///` prefisso del percorso, ad esempio:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "L'URL del servizio web può essere un'origine remota ospitato o risorsa di sistema di file locale accessibile tramite il prefisso del percorso file")](images/add-webreference-dialog.png#lightbox)

Il proxy verrà generato nella cartella del progetto Web o i riferimenti al servizio. Poiché viene generato un proxy di codice, non deve essere modificato.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Aggiunta manuale di un Proxy a un progetto

Se si dispone di un proxy esistente è stato generato usando gli strumenti compatibili, questo output può essere utilizzato se incluse come parte del progetto. In Visual Studio per Mac, usare il **aggiungere i file...** opzione di menu per aggiungere il proxy. Inoltre, questa operazione richiede *DLL* a cui fare riferimento in modo esplicito utilizzando il **aggiungere riferimenti...** finestra di dialogo.

### <a name="consuming-the-proxy"></a>Utilizzo del Proxy

Le classi proxy generate forniscono metodi per l'utilizzo del servizio web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello di un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Esempio di codice seguente illustra un esempio:

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

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `Task.Factory.FromAsync` (metodo). Questo metodo crea un `Task` che consente di eseguire il `TodoService.EndGetTodoItems` metodo una volta il `TodoService.BeginGetTodoItems` metodo viene completato, con il `null` parametro che indica che nessun dato vengono passato nel `BeginGetTodoItems` delegare. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Per altre informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e tradizionale programmazione asincrona .NET Framework](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

Per altre informazioni sull'utilizzo di un servizio ASMX, vedere [utilizzo di un servizio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF è un framework unificato di Microsoft per la compilazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite sicure, affidabili, transazionali e interoperabile.

WCF descrive un servizio con un'ampia gamma di contratti diversi che includono i seguenti:

- **I contratti dati** : definire le strutture di dati che costituiscono la base per il contenuto all'interno di un messaggio.
- **Contratti di messaggio** : comporre i messaggi da contratti dati esistenti.
- **I contratti di errore** -Consenti errori SOAP personalizzati essere specificato.
- **I contratti di servizio** : specificare le operazioni che supportano servizi e i messaggi necessari per l'interazione con ogni operazione. Specificano anche eventuali comportamenti di errore personalizzato che possono essere associato a operazioni su ogni servizio.

Esistono differenze tra servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità offerte da ASMX: messaggi SOAP su HTTP.

> [!IMPORTANT]
> Il supporto della piattaforma Xamarin per WCF è limitato ai messaggi SOAP con codifica del testo rispetto all'uso di HTTP/HTTPS di `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso degli strumenti disponibili solo in un ambiente di Windows per generare il proxy.

### <a name="generating-a-proxy"></a>Generazione di un Proxy

Oggetto *proxy* deve essere generato per l'utilizzo di un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definiscono i metodi e la configurazione del servizio associato. Questi metadati viene esposta sotto forma di un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy può essere compilato usando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio web a una libreria .NET Standard.

Un'alternativa alla creazione del proxy utilizzando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 è usare la strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per altre informazioni, vedere [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configurazione del Proxy

Configurazione del proxy generato in genere richiederà due argomenti di configurazione (a seconda della SOAP 1.1/ASMX o WCF) durante l'inizializzazione: il `EndpointAddress` e/o le relative informazioni sul binding, come illustrato nell'esempio seguente:

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

### <a name="consuming-the-proxy"></a>Utilizzo del Proxy

Le classi proxy generate forniscono metodi per l'uso dei servizi web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Esempio di codice seguente illustra un esempio:

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

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `Task.Factory.FromAsync` (metodo). Questo metodo crea un `Task` che consente di eseguire il `TodoServiceClient.EndGetTodoItems` metodo una volta il `TodoServiceClient.BeginGetTodoItems` metodo viene completato, con il `null` parametro che indica che nessun dato vengono passato nel `BeginGetTodoItems` delegare. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Per altre informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e tradizionale programmazione asincrona .NET Framework](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

Per altre informazioni sull'utilizzo di un servizio WCF, vedere [utilizzo di un servizio Web di Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Sicurezza del trasporto

Servizi WCF possono utilizzare la sicurezza a livello di trasporto per proteggere l'intercettazione dei messaggi. La piattaforma Xamarin supporta le associazioni che si avvalgono di sicurezza a livello di trasporto utilizza SSL. Tuttavia, potrebbero esserci casi in cui lo stack potrebbe essere necessario convalidare il certificato, con conseguente comportamento inaspettato. La convalida può essere sottoposto a override registrando un `ServerCertificateValidationCallback` delegato prima di richiamare il servizio, come illustrato nell'esempio di codice seguente:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

In questo modo viene mantenuta la crittografia di trasporto, ignorando la convalida del certificato sul lato server. Tuttavia, in modo efficace questo approccio non considera i problemi di attendibilità associati al certificato e potrebbe non essere appropriato. Per altre informazioni, vedere [usando Trusted osservanza radici](https://www.mono-project.com/UsingTrustedRootsRespectfully) sul [mono-project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Utilizza la sicurezza delle credenziali Client

Servizi WCF possono inoltre richiedere i client di servizio per l'autenticazione utilizzando le credenziali. La piattaforma Xamarin non supporta il protocollo WS-Security, che consente ai client di inviare le credenziali all'interno la busta del messaggio SOAP. Tuttavia, la piattaforma Xamarin supporta la possibilità di inviare le credenziali di autenticazione di base HTTP al server, specificando l'oggetto appropriato `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Quindi, è possibile specificare le credenziali di autenticazione di base:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Nell'esempio precedente, se viene visualizzato il messaggio "Ha esaurito trampolines di tipo 0" è possibile aumentare il numero di trampolines tipo 0 aggiungendo il `–aot “trampolines={number of trampolines}”` argomento per la compilazione. Per altre informazioni, vedere [Risoluzione dei problemi](~/ios/troubleshooting/troubleshooting.md#trampolines).

Per altre informazioni sull'autenticazione di base HTTP, anche se nel contesto di un servizio web REST, vedere [autenticare un servizio Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Collegamenti correlati

- [Servizi Web in xamarin. Forms](~/xamarin-forms/data-cloud/index.md)
- [Strumento ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
