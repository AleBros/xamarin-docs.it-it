---
title: Utilizzare un servizio Web di Windows Communication Foundation (WCF)
description: Questo articolo illustra come usare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 28cb1573262b63cc2b0ccad9f468fe36c682718d
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915373"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Utilizzare un servizio Web di Windows Communication Foundation (WCF)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF è il Framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di creare applicazioni distribuite sicure, affidabili, transazionali e interoperabili. In questo articolo viene illustrato come utilizzare un servizio WCF Simple Object Access Protocol (SOAP) da un'applicazione Novell. Forms._

WCF descrive un servizio con un'ampia gamma di contratti diversi, tra cui:

- **Contratti dati** : definiscono le strutture di dati che costituiscono la base per il contenuto in un messaggio.
- **Contratti di messaggio** : comporre messaggi da contratti dati esistenti.
- **Contratti di errore** : consente di specificare errori SOAP personalizzati.
- **Contratti di servizio** : specificare le operazioni supportate dai servizi e i messaggi necessari per interagire con ogni operazione. Specificano anche eventuali comportamenti di errore personalizzato che possono essere associato a operazioni su ogni servizio.

Esistono differenze tra i servizi Web ASP.NET (ASMX) e WCF, ma WCF supporta le stesse funzionalità fornite da ASMX, ovvero messaggi SOAP su HTTP. Per ulteriori informazioni sull'utilizzo di un servizio ASMX, vedere la pagina relativa all'utilizzo di [ASP.NET Web Services (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> Il supporto della piattaforma Novell per WCF è limitato ai messaggi SOAP con codifica testo tramite HTTP/HTTPS usando la classe `BasicHttpBinding`.
>
> Il supporto WCF richiede l'uso di strumenti disponibili solo in un ambiente Windows per generare il proxy e ospitare il TodoWCFService. La compilazione e il test dell'app iOS richiederanno la distribuzione di TodoWCFService in un computer Windows o come servizio Web di Azure.
>
> Le app Novell Forms native condividono in genere il codice con una libreria di classi .NET Standard. Tuttavia, .NET Core non supporta attualmente WCF, pertanto il progetto condiviso deve essere una libreria di classi portabile legacy. Per informazioni sul supporto di WCF in .NET Core, vedere [scelta tra .NET Core e .NET Framework per le app Server](/dotnet/standard/choosing-core-framework-server).

La soluzione dell'applicazione di esempio include un servizio WCF che può essere eseguito localmente ed è illustrato nello screenshot seguente:

![](wcf-images/portal.png "Sample Application")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
>
> È possibile escludere ATS da se non è possibile usare il protocollo `HTTPS` e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Utilizzare il servizio Web

Il servizio WCF fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività|Una stringa XML serializzata TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Una stringa XML serializzata TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Una stringa XML serializzata TodoItem|

Per ulteriori informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/web-services/introduction.md).

È necessario generare un *proxy* per utilizzare un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definiscono i metodi e la configurazione del servizio associato. Questi metadati viene esposta sotto forma di un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy può essere compilato usando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio web a una libreria .NET Standard. Un'alternativa alla creazione del proxy utilizzando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 è usare la strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per ulteriori informazioni, vedere [ServiceModel Metadata Utility Tool (Svcutil. exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Le classi proxy generate forniscono metodi per l'uso dei servizi web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il metodo *BeginOperationName* avvia l'operazione asincrona e restituisce un oggetto che implementa l'interfaccia `IAsyncResult`. Dopo la chiamata a *BeginOperationName*, un'applicazione può continuare a eseguire le istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un thread del pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve anche chiamare *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo di servizio Web sincrono. Il metodo `EndGetTodoItems`, ad esempio, restituisce una raccolta di istanze di `TodoItem`. Il metodo *EndOperationName* include anche un parametro `IAsyncResult` che deve essere impostato sull'istanza restituita dalla chiamata corrispondente al metodo *BeginOperationName* .

Il Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi Begin/End APM incapsulando le operazioni asincrone nello stesso oggetto `Task`. Questo incapsulamento viene fornito da più overload del metodo `TaskFactory.FromAsync`.

Per ulteriori informazioni su APM, vedere il [modello di programmazione asincrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e la tradizionale .NET Framework la programmazione asincrona](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

### <a name="create-the-todoserviceclient-object"></a>Creare l'oggetto TodoServiceClient

La classe proxy generata fornisce la classe `TodoServiceClient`, che viene utilizzata per comunicare con il servizio WCF tramite HTTP. Fornisce funzionalità per richiamare i metodi del servizio web come operazioni asincrone da un URI identificato l'istanza del servizio. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto](~/cross-platform/platform/async.md)asincrono.

L'istanza `TodoServiceClient` viene dichiarata a livello di classe in modo che l'oggetto sia disponibile fino a quando l'applicazione deve utilizzare il servizio WCF, come illustrato nell'esempio di codice seguente:

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

L'istanza di `TodoServiceClient` viene configurata con le informazioni di associazione e un indirizzo endpoint. Viene utilizzata un'associazione per specificare il trasporto, codifica e i dettagli di protocollo necessari per le applicazioni e servizi comunicare tra loro. Il `BasicHttpBinding` specifica che i messaggi SOAP con codifica testo verranno inviati tramite il protocollo di trasporto HTTP. Specifica un indirizzo endpoint consente all'applicazione di connettersi alle diverse istanze del servizio WCF, condizione che sono presenti più istanze pubblicate.

Per ulteriori informazioni sulla configurazione del riferimento al servizio, vedere [configurazione del riferimento al servizio](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Creare oggetti di trasferimento dati

Nell'applicazione di esempio viene utilizzata la classe `TodoItem` per modellare i dati. Per archiviare un elemento `TodoItem` nel servizio Web, è necessario innanzitutto convertirlo nel proxy generato `TodoItem` tipo. Questa operazione viene eseguita dal metodo `ToWCFServiceTodoItem`, come illustrato nell'esempio di codice seguente:

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

Questo metodo crea semplicemente una nuova istanza di `TodoWCFService.TodoItem` e imposta ogni proprietà sulla proprietà identica dall'istanza di `TodoItem`.

Analogamente, quando i dati vengono recuperati dal servizio Web, è necessario convertirli dal proxy generato `TodoItem` tipo in un'istanza di `TodoItem`. Questa operazione viene eseguita con il metodo `FromWCFServiceTodoItem`, come illustrato nell'esempio di codice seguente:

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

Questo metodo recupera semplicemente i dati dal proxy generato `TodoItem` tipo e lo imposta nell'istanza di `TodoItem` appena creata.

### <a name="retrieve-data"></a>Recuperare i dati

I metodi `TodoServiceClient.BeginGetTodoItems` e `TodoServiceClient.EndGetTodoItems` vengono utilizzati per chiamare l'operazione `GetTodoItems` fornita dal servizio Web. Questi metodi asincroni sono incapsulati in un oggetto `Task`, come illustrato nell'esempio di codice seguente:

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

Il metodo `Task.Factory.FromAsync` crea una `Task` che esegue il metodo `TodoServiceClient.EndGetTodoItems` al termine del `TodoServiceClient.BeginGetTodoItems` metodo con il parametro `null` che indica che non è stato passato alcun dato al delegato `BeginGetTodoItems`. Infine, il valore dell'enumerazione `TaskCreationOptions` specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione delle attività.

Il metodo `TodoServiceClient.EndGetTodoItems` restituisce un `ObservableCollection` di istanze di `TodoWCFService.TodoItem`, che viene quindi convertito in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="create-data"></a>Creazione di dati

I metodi `TodoServiceClient.BeginCreateTodoItem` e `TodoServiceClient.EndCreateTodoItem` vengono utilizzati per chiamare l'operazione `CreateTodoItem` fornita dal servizio Web. Questi metodi asincroni sono incapsulati in un oggetto `Task`, come illustrato nell'esempio di codice seguente:

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

Il metodo `Task.Factory.FromAsync` crea una `Task` che esegue il metodo `TodoServiceClient.EndCreateTodoItem` una volta completato il `TodoServiceClient.BeginCreateTodoItem` metodo, con il `todoItem` parametro che è costituito dai dati passati al delegato `BeginCreateTodoItem` per specificare il `TodoItem` da creare con il servizio Web. Infine, il valore dell'enumerazione `TaskCreationOptions` specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione delle attività.

Il servizio Web genera un'`FaultException` se non riesce a creare il `TodoItem`, gestito dall'applicazione.

### <a name="update-data"></a>Aggiornare i dati

I metodi `TodoServiceClient.BeginEditTodoItem` e `TodoServiceClient.EndEditTodoItem` vengono utilizzati per chiamare l'operazione `EditTodoItem` fornita dal servizio Web. Questi metodi asincroni sono incapsulati in un oggetto `Task`, come illustrato nell'esempio di codice seguente:

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

Il metodo `Task.Factory.FromAsync` crea una `Task` che esegue il metodo `TodoServiceClient.EndEditTodoItem` una volta completato il `TodoServiceClient.BeginCreateTodoItem` metodo, con il `todoItem` parametro che è costituito dai dati passati al delegato `BeginEditTodoItem` per specificare il `TodoItem` da aggiornare al servizio Web. Infine, il valore dell'enumerazione `TaskCreationOptions` specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione delle attività.

Il servizio Web genera un'`FaultException` se non riesce a individuare o aggiornare il `TodoItem`, gestito dall'applicazione.

### <a name="delete-data"></a>Eliminare i dati

I metodi `TodoServiceClient.BeginDeleteTodoItem` e `TodoServiceClient.EndDeleteTodoItem` vengono utilizzati per chiamare l'operazione `DeleteTodoItem` fornita dal servizio Web. Questi metodi asincroni sono incapsulati in un oggetto `Task`, come illustrato nell'esempio di codice seguente:

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

Il metodo `Task.Factory.FromAsync` crea una `Task` che esegue il metodo `TodoServiceClient.EndDeleteTodoItem` una volta completato il `TodoServiceClient.BeginDeleteTodoItem` metodo, con il parametro `id` i dati passati al delegato `BeginDeleteTodoItem` per specificare il `TodoItem` da eliminare dal servizio Web. Infine, il valore dell'enumerazione `TaskCreationOptions` specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione delle attività.

Il servizio Web genera un'`FaultException` se non riesce a individuare o eliminare il `TodoItem`, gestito dall'applicazione.

## <a name="configure-remote-access-to-iis-express"></a>Configurare l'accesso remoto per IIS Express
In Visual Studio 2017 o Visual Studio 2019 dovrebbe essere possibile testare l'applicazione UWP in un PC senza alcuna configurazione aggiuntiva. Il test dei client Android e iOS può richiedere i passaggi aggiuntivi in questa sezione. Per altre informazioni, vedere [connettersi ai servizi Web locali da simulatori iOS e emulatori Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) .

Per impostazione predefinita, IIS Express risponderà solo alle richieste di `localhost`. I dispositivi remoti (ad esempio un dispositivo Android, un iPhone o anche un simulatore) non avranno accesso al servizio WCF locale. È necessario che l'indirizzo IP della workstation Windows 10 sia presente nella rete locale. Ai fini di questo esempio, si supponga che la workstation disponga dell'indirizzo IP `192.168.1.143`. Nei passaggi seguenti viene illustrato come configurare Windows 10 e IIS Express per accettare le connessioni remote e connettersi al servizio da un dispositivo fisico o virtuale:

1. **Aggiungere un'eccezione a Windows Firewall**. Per comunicare con il servizio WCF, è necessario aprire una porta tramite Windows Firewall che le applicazioni nella subnet possano utilizzare. Creare una regola in ingresso che apre la porta 49393 nel firewall. Da un prompt dei comandi amministrativo eseguire questo comando:

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurare IIS Express per accettare le connessioni remote**. È possibile configurare IIS Express modificando il file di configurazione per IIS Express in **[directory soluzione]\.vs\config\applicationhost.config**. Trovare l'elemento `site` con il nome `TodoWCFService`. Dovrebbe essere simile al codice XML seguente:

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

    Sarà necessario aggiungere due elementi `binding` per aprire la porta 49393 al traffico esterno e all'emulatore Android. Il binding utilizza un formato `[IP address]:[port]:[hostname]` che specifica il modo in cui IIS Express risponderà alle richieste. Le richieste esterne avranno nomi host che devono essere specificati come `binding`. Aggiungere il codice XML seguente all'elemento `bindings`, sostituendo l'indirizzo IP con il proprio indirizzo IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Una volta apportate le modifiche, l'elemento `bindings` dovrebbe essere simile al seguente:

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
    >Per impostazione predefinita, IIS Express non accetterà connessioni da origini esterne per motivi di sicurezza. Per abilitare le connessioni da dispositivi remoti, è necessario eseguire IIS Express con autorizzazioni amministrative. Il modo più semplice per eseguire questa operazione consiste nell'eseguire Visual Studio 2017 con autorizzazioni amministrative. Verrà avviata IIS Express con autorizzazioni amministrative quando si esegue TodoWCFService.

    Una volta completati questi passaggi, dovrebbe essere possibile eseguire TodoWCFService e connettersi da altri dispositivi nella subnet. Per eseguire questa verifica, è possibile eseguire l'applicazione e visitare `http://localhost:49393/TodoService.svc`. Se si verifica un errore di **richiesta non valida** quando si visita tale URL, il `bindings` potrebbe non essere corretto nella configurazione IIS Express (la richiesta sta raggiungendo IIS Express ma verrà rifiutata). Se si riceve un errore diverso, è possibile che l'applicazione non sia in esecuzione o che il firewall non sia configurato correttamente.

    Per consentire a IIS Express di continuare a eseguire e servire il servizio, disattivare l'opzione **modifica e continuazione** nelle **proprietà del progetto > debugger > Web**.

1. **Personalizzare i dispositivi endpoint da usare per accedere al servizio**. Questo passaggio prevede la configurazione dell'applicazione client, in esecuzione su un dispositivo fisico o emulato, per accedere al servizio WCF.

    L'emulatore Android usa un proxy interno che impedisce all'emulatore di accedere direttamente all'indirizzo `localhost` del computer host. Al contrario, l'indirizzo `10.0.2.2` sull'emulatore viene instradato a `localhost` nel computer host tramite un proxy interno. Queste richieste con proxy avranno `127.0.0.1` come nome host nell'intestazione della richiesta, motivo per cui è stata creata la IIS Express binding per questo nome host nei passaggi precedenti.

    Il simulatore iOS viene eseguito in un host di compilazione Mac, anche se si usa il [simulatore iOS remoto per Windows](~/tools/ios-simulator/index.md). Le richieste di rete del simulatore avranno l'IP della workstation nella rete locale come nome host (in questo esempio è `192.168.1.143`, ma l'indirizzo IP effettivo sarà probabilmente diverso). Questo è il motivo per cui è stata creata la IIS Express binding per questo nome host nei passaggi precedenti.

    Verificare che la proprietà `SoapUrl` nel file **Constants.cs** nel progetto TodoWCF (Portable) includa valori corretti per la rete:

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

    Dopo aver configurato il **Constants.cs** con gli endpoint appropriati, dovrebbe essere possibile connettersi al TodoWCFService in esecuzione nella workstation Windows 10 da dispositivi fisici o virtuali.

## <a name="related-links"></a>Collegamenti correlati

- [TodoWCF (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [Procedura: creare un client di Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [ServiceModel Metadata Utility Tool (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
