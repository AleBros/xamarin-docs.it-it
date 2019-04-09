---
title: Utilizzare un servizio Web di Windows Communication Foundation (WCF)
description: Questo articolo illustra come usare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 7106c0aed03800d3479471caab0974be3c09c1f8
ms.sourcegitcommit: cc750b0d8086ed14f84cd8eb9a06f45c719b3cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59239914"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Utilizzare un servizio Web di Windows Communication Foundation (WCF)

[![Download esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_WCF è un framework unificato di Microsoft per la compilazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite sicure, affidabili, transazionali e interoperabile. Questo articolo illustra come usare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione xamarin. Forms._

WCF descrive un servizio con un'ampia gamma di contratti diversi tra cui:

- **I contratti dati** : definire le strutture di dati che costituiscono la base per il contenuto all'interno di un messaggio.
- **Contratti di messaggio** : comporre i messaggi da contratti dati esistenti.
- **I contratti di errore** -Consenti errori SOAP personalizzati essere specificato.
- **I contratti di servizio** : specificare le operazioni che supportano servizi e i messaggi necessari per l'interazione con ogni operazione. Specificano anche eventuali comportamenti di errore personalizzato che possono essere associato a operazioni su ogni servizio.

Esistono differenze tra servizi Web ASP.NET (ASMX) e WCF, tuttavia WCF supporta le stesse funzionalità offerte da ASMX: messaggi SOAP su HTTP. Per altre informazioni sull'utilizzo di un servizio ASMX, vedere [utilizzo di servizi Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

> [!IMPORTANT]
> Il supporto della piattaforma Xamarin per WCF è limitato ai messaggi SOAP con codifica del testo rispetto all'uso di HTTP/HTTPS di `BasicHttpBinding` classe.
>
> Supporto WCF richiede l'uso di strumenti disponibili solo in un ambiente di Windows per generare il proxy e ospitare il TodoWCFService. Compilazione e test dell'app iOS richiederanno distribuzione TodoWCFService in un computer Windows o come servizio web di Azure.
>
> App native di Xamarin. Forms, in genere, condividere codice con una libreria di classi .NET Standard. Tuttavia, .NET Core non supporta attualmente WCF in modo che il progetto condiviso deve essere una libreria di classi portabile legacy. Per informazioni sul supporto WCF in .NET Core, vedere [scelta tra .NET Core e .NET Framework per le app server](/dotnet/standard/choosing-core-framework-server).

La soluzione dell'applicazione di esempio include un servizio WCF che può essere eseguito in locale e viene illustrato nello screenshot seguente:

![](wcf-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
>
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Utilizzare il servizio web

Il servizio WCF fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività|Una stringa XML serializzata TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Una stringa XML serializzata TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Una stringa XML serializzata TodoItem|

Per altre informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/walkthrough.md).

Oggetto *proxy* deve essere generato per l'utilizzo di un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definiscono i metodi e la configurazione del servizio associato. Questi metadati viene esposta sotto forma di un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy può essere compilato usando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio web a una libreria .NET Standard. Un'alternativa alla creazione del proxy utilizzando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 è usare la strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per altre informazioni, vedere [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Le classi proxy generate forniscono metodi per l'uso dei servizi web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Ad esempio, il `EndGetTodoItems` metodo restituisce una raccolta di `TodoItem` istanze. Il *Endnomeoperazione* metodo include anche un' `IAsyncResult` che deve essere impostato per l'istanza restituita dalla chiamata corrispondente al parametro il *Beginnomeoperazione* (metodo).

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `TaskFactory.FromAsync` (metodo).

Per altre informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e tradizionale programmazione asincrona .NET Framework](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

### <a name="create-the-todoserviceclient-object"></a>Creare l'oggetto TodoServiceClient

Fornisce la classe proxy generata la `TodoServiceClient` (classe), che consente di comunicare con il servizio WCF tramite HTTP. Fornisce funzionalità per richiamare i metodi del servizio web come operazioni asincrone da un URI identificato l'istanza del servizio. Per altre informazioni sulle operazioni asincrone, vedere [Panoramica del supporto asincrono](~/cross-platform/platform/async.md).

Il `TodoServiceClient` istanza viene dichiarata a livello di classe in modo che l'oggetto dura per fino a quando l'applicazione deve utilizzare il servizio WCF, come illustrato nell'esempio di codice seguente:

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

Il `TodoServiceClient` istanza viene configurata con un indirizzo endpoint e informazioni di binding. Viene utilizzata un'associazione per specificare il trasporto, codifica e i dettagli di protocollo necessari per le applicazioni e servizi comunicare tra loro. Il `BasicHttpBinding` specifica che verranno inviati i messaggi SOAP con codifica del testo tramite il protocollo di trasporto HTTP. Specifica un indirizzo endpoint consente all'applicazione di connettersi alle diverse istanze del servizio WCF, condizione che sono presenti più istanze pubblicate.

Per altre informazioni sulla configurazione di riferimento al servizio, vedere [configurare il riferimento al servizio](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Creare gli oggetti di trasferimento dei dati

L'applicazione di esempio Usa il `TodoItem` classe ai dati del modello. Per archiviare una `TodoItem` elemento nel servizio web è necessario prima convertirlo in proxy generato `TodoItem` tipo. Questa operazione viene eseguita la `ToWCFServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Questo metodo crea semplicemente una nuova `TodoWCFService.TodoItem` dell'istanza e imposta ogni proprietà per la proprietà identica dal `TodoItem` istanza.

Analogamente, quando i dati vengono recuperati dal servizio web, è necessario convertirlo dal proxy generato `TodoItem` tipo di un `TodoItem` istanza. Questa operazione viene eseguita con il `FromWCFServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Questo metodo recupera semplicemente i dati dal proxy generato `TodoItem` tipo e lo imposta appena creato `TodoItem` istanza.

### <a name="retrieve-data"></a>Recupero dei dati

Il `TodoServiceClient.BeginGetTodoItems` e `TodoServiceClient.EndGetTodoItems` vengono utilizzati metodi per chiamare il `GetTodoItems` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoServiceClient.EndGetTodoItems` metodo una volta il `TodoServiceClient.BeginGetTodoItems` metodo viene completato, con il `null` parametro che indica che nessun dato vengono passato nel `BeginGetTodoItems` delegare. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il `TodoServiceClient.EndGetTodoItems` metodo restituisce un `ObservableCollection` dei `TodoWCFService.TodoItem` istanze, che viene quindi convertito in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="create-data"></a>Creare i dati

Il `TodoServiceClient.BeginCreateTodoItem` e `TodoServiceClient.EndCreateTodoItem` vengono utilizzati metodi per chiamare il `CreateTodoItem` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un' `Task` che esegue il `TodoServiceClient.EndCreateTodoItem` metodo una volta il `TodoServiceClient.BeginCreateTodoItem` metodo viene completato, con il `todoItem` in corso i dati che viene passati nel parametro il `BeginCreateTodoItem` delegato per specificare il `TodoItem` deve essere creato dal servizio web. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il servizio web genera un'eccezione una `FaultException` se non riesce a creare il `TodoItem`, che viene gestita dall'applicazione.

### <a name="update-data"></a>aggiornare i dati

Il `TodoServiceClient.BeginEditTodoItem` e `TodoServiceClient.EndEditTodoItem` vengono utilizzati metodi per chiamare il `EditTodoItem` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un' `Task` che esegue il `TodoServiceClient.EndEditTodoItem` metodo una volta il `TodoServiceClient.BeginCreateTodoItem` metodo viene completato, con il `todoItem` in corso i dati che viene passati nel parametro il `BeginEditTodoItem` delegato per specificare il `TodoItem` da aggiornare tramite il servizio web. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il servizio web genera un'eccezione una `FaultException` se non riesce a individuare o aggiornare il `TodoItem`, che viene gestita dall'applicazione.

### <a name="delete-data"></a>Eliminare i dati

Il `TodoServiceClient.BeginDeleteTodoItem` e `TodoServiceClient.EndDeleteTodoItem` vengono utilizzati metodi per chiamare il `DeleteTodoItem` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un' `Task` che esegue il `TodoServiceClient.EndDeleteTodoItem` metodo una volta il `TodoServiceClient.BeginDeleteTodoItem` metodo viene completato, con il `id` in corso i dati che viene passati nel parametro il `BeginDeleteTodoItem` delegato per specificare il `TodoItem` deve essere eliminato dal servizio web. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il servizio web genera un'eccezione una `FaultException` se non riesce a trovare o eliminare il `TodoItem`, che viene gestita dall'applicazione.

## <a name="configure-remote-access-to-iis-express"></a>Configurare l'accesso remoto a IIS Express
In Visual Studio 2017 o Visual Studio 2019, dovrebbe essere possibile testare l'applicazione UWP in un computer senza alcuna configurazione aggiuntiva. Testare i client Android e iOS possono richiedere i passaggi aggiuntivi in questa sezione. Visualizzare [connettersi a servizi Web locali da iOS, Android emulatori e simulatori](~/cross-platform/deploy-test/connect-to-local-web-services.md) per altre informazioni.

Per impostazione predefinita, IIS Express risponderà solo alle richieste di `localhost`. Dispositivi remoti (ad esempio un dispositivo Android, un iPhone o persino un simulatore) non saranno possibile accedere al servizio WCF locale. È necessario conoscere l'indirizzo IP di workstation di Windows 10 nella rete locale. Ai fini di questo esempio, si supponga che la workstation abbia l'indirizzo IP `192.168.1.143`. I passaggi seguenti illustrano come configurare Windows 10 e IIS Express per accettare le connessioni remote e connettersi al servizio da un dispositivo fisico o virtuale:

1. **Aggiungere un'eccezione al Firewall Windows**. È necessario aprire una porta attraverso Windows Firewall che applicazioni sulla subnet possono usare per comunicare con il servizio WCF. Creare una regola in ingresso, aprire la porta 49393 nel firewall. Da un prompt dei comandi amministrativo, eseguire questo comando:
    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurare IIS Express per le connessioni Remote accettare**. È possibile configurare IIS Express modificando il file di configurazione per IIS Express **[directory soluzione]\.vs\config\applicationhost.config**. Trovare il `site` elemento con il nome `TodoWCFService`. Dovrebbe essere simile al codice XML seguente:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    È necessario aggiungere due `binding` elementi per aprire la porta 49393 al traffico esterno e l'emulatore di Android. L'associazione utilizza una `[IP address]:[port]:[hostname]` formato che specifica come IIS Express risponderà alle richieste. Le richieste esterne avranno nomi host che deve essere specificato come un `binding`. Aggiungere il seguente codice XML per il `bindings` elemento, sostituendo l'indirizzo IP con il proprio indirizzo IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Dopo le modifiche di `bindings` elemento dovrebbe essere simile al seguente:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >Per impostazione predefinita, IIS Express non accetterà le connessioni provenienti da origini esterne per motivi di sicurezza. Per abilitare le connessioni da dispositivi remoti è necessario eseguire IIS Express con autorizzazioni amministrative. Il modo più semplice per eseguire questa operazione consiste nell'eseguire Visual Studio 2017 con autorizzazioni amministrative. Si avvierà IIS Express con autorizzazioni amministrative durante l'esecuzione di TodoWCFService.

    Con la procedura completa, dovrebbe essere possibile eseguire il TodoWCFService e connettersi da altri dispositivi sulla subnet. È possibile verificarlo eseguendo l'applicazione e che visitano `http://localhost:49393/TodoService.svc`. Se si verifica una **Bad Request** errore durante la visita dell'URL, il `bindings` potrebbe non essere corretto nella configurazione di IIS Express (la richiesta sta per raggiungere IIS Express ma è stata rifiutata). Se si verifica un errore diverso, che è possibile che l'applicazione non è in esecuzione o il firewall sia configurato in modo non corretto.

    Per consentire a IIS Express mantenere in esecuzione e la gestione del servizio, disattivare la **modifica e continuazione** opzione **proprietà progetto > Web > debugger**.

1. **Personalizzare l'endpoint di dispositivi usano per accedere al servizio**. Questo passaggio prevede la configurazione dell'applicazione client in esecuzione in un dispositivo fisico o emulato, per accedere al servizio WCF.

    L'emulatore di Android Usa un proxy interno che impedisce l'accesso diretto del computer host dell'emulatore `localhost` indirizzo. Al contrario, l'indirizzo `10.0.2.2` nell'emulatore viene indirizzato a `localhost` nel computer host tramite un proxy interno. Queste richieste trasmesse tramite proxy avrà `127.0.0.1` come il nome host nell'intestazione della richiesta, che è il motivo per cui è stato creato il binding IIS Express per il nome host nei passaggi precedenti.

    IOS simulatore viene eseguito su un Mac build host, anche se si usa la [iOS remoto simulatore per Windows](~/tools/ios-simulator/index.md). Le richieste di rete dal simulatore saranno necessario l'indirizzo IP workstation sulla rete come il nome host locale (in questo esempio ha `192.168.1.143`, ma l'indirizzo IP effettivo sarà probabilmente diverso). Ecco perché il binding IIS Express per il nome host è stato creato nei passaggi precedenti.

    Verificare che il `SoapUrl` proprietà nel **Constants.cs** file nel progetto TodoWCF (portabile) hanno valori corretti per la rete:

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    Dopo aver configurato il **Constants.cs** con gli endpoint appropriati, deve essere in grado di connettersi al TodoWCFService in esecuzione sulla workstation di Windows 10 da dispositivi fisici o virtuali.

## <a name="related-links"></a>Collegamenti correlati

- [TodoWCF (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [Procedura: Creare un Client Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Strumento ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
