---
title: Utilizzo di un servizio Web di Windows Communication Foundation (WCF)
description: In questo articolo viene illustrato come utilizzare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 05092a3648ac4c37dfd8d712184176a544979ede
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241204"
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>Utilizzo di un servizio Web di Windows Communication Foundation (WCF)

_WCF è framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite protette, affidabile, transazioni e interoperative. In questo articolo viene illustrato come utilizzare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione di xamarin. Forms._

WCF viene descritto un servizio con un'ampia gamma di diversi contratti che includono i seguenti:

- **Contratti dati** : definire le strutture di dati che costituiscono la base per il contenuto all'interno di un messaggio.
- **Contratti di messaggio** : scrivere messaggi dai contratti dati esistenti.
- **Contratti di errore** : consente di specificare gli errori personalizzati di SOAP.
- **Contratti di servizio** : specificare le operazioni che supportano servizi e i messaggi necessari per l'interazione con ogni operazione. Specificano inoltre eventuali comportamenti di errore personalizzato che possono essere associato a operazioni in ogni servizio.

Esistono differenze tra i servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità che fornisce ASMX: messaggi SOAP su HTTP. Per ulteriori informazioni sull'utilizzo di un servizio ASMX, vedere [utilizzano servizi Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

In generale, sulla piattaforma Xamarin supporta lo stesso subset di sul lato client di WCF che viene fornito con il runtime Silverlight. Ciò include le implementazioni più comuni di codifica e protocollo di WCF, ovvero codificata in formato testo di messaggi SOAP su HTTP tramite protocollo di trasporto di `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso di strumenti disponibili solo in un ambiente di Windows per generare il proxy.

Istruzioni sull'impostazione del servizio WCF sono reperibile nel file Leggimi che accompagna l'applicazione di esempio. Tuttavia, quando viene eseguita l'applicazione di esempio si connetterà a un servizio WCF ospitato di Xamarin che fornisce l'accesso in sola lettura ai dati, come illustrato nella schermata seguente:

![](wcf-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio WCF fornisce le seguenti operazioni:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività da eseguire|Un codice XML serializzato TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Un codice XML serializzato TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Un codice XML serializzato TodoItem|

Per ulteriori informazioni sul modello di data utilizzato nell'applicazione, vedere [modellazione dati](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> L'applicazione di esempio utilizza il servizio WCF ospitato di Xamarin che fornisce l'accesso in sola lettura per il servizio web. Le operazioni di creano, aggiornano ed eliminare dati, pertanto, non eliminerà i dati utilizzati nell'applicazione. È tuttavia disponibile in una versione eseguibile del servizio ASMX il **TodoWCFService** cartella nell'applicazione di esempio associato. Questa versione eseguibile di completo i permessi di servizio WCF di creare, aggiornare, leggere ed elimina l'accesso ai dati.

Oggetto *proxy* deve essere generato per utilizzare un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito mediante dispendiosa in termini di metadati del servizio che definiscono i metodi e la configurazione del servizio associato. I metadati vengono esposti nel formato di un documento Web Services Description Language (WSDL) che viene generato dal servizio web. Il proxy può essere compilato usando il Provider di riferimento del servizio di Microsoft WCF Web in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio web a una libreria .NET Standard. Un'alternativa alla creazione del proxy utilizzando Provider riferimento del servizio Web di Microsoft WCF in Visual Studio 2017 consiste nell'utilizzare lo strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per ulteriori informazioni, vedere [strumento ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Le classi proxy generato forniscono metodi per l'utilizzo dei servizi web che utilizzano il modello di progettazione del modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il *BeginOperationName* metodo inizia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo la chiamata *BeginOperationName*, un'applicazione può continuare l'esecuzione di istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve chiamare anche *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo del servizio web sincrono. Ad esempio, il `EndGetTodoItems` il metodo restituisce una raccolta di `TodoItem` istanze. Il *EndOperationName* metodo include anche un `IAsyncResult` parametro che deve essere impostato per l'istanza restituita dalla chiamata corrispondente al *BeginOperationName* metodo.

Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nello stesso `Task` oggetto. Fornito da più overload di questo incapsulamento di `TaskFactory.FromAsync` metodo.

Per ulteriori informazioni su APM vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e .NET Framework programmazione asincrona tradizionale](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

### <a name="creating-the-todoserviceclient-object"></a>Creazione dell'oggetto TodoServiceClient

Fornisce la classe proxy generata la `TodoServiceClient` classe, che viene utilizzato per comunicare con il servizio WCF tramite HTTP. Fornisce funzionalità per richiamare i metodi del servizio web come operazioni asincrone da un URI identificato l'istanza del servizio. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto di Async](~/cross-platform/platform/async.md).

Il `TodoServiceClient` istanza viene dichiarata a livello di classe in modo che l'oggetto ha una validità fino a quando l'applicazione deve utilizzare il servizio WCF, come illustrato nell'esempio di codice seguente:

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

Il `TodoServiceClient` istanza è configurata con un indirizzo endpoint e informazioni di associazione. Un'associazione viene utilizzata per specificare il trasporto, codifica e protocollo dettagli necessari per le applicazioni e servizi comunicare tra loro. Il `BasicHttpBinding` specifica che i messaggi SOAP con codifica testo verranno inviati tramite il protocollo di trasporto HTTP. Specifica un indirizzo endpoint consente all'applicazione per la connessione tra le diverse istanze del servizio WCF, purché non ci siano più istanze pubblicate.

Per ulteriori informazioni sulla configurazione il riferimento al servizio, vedere [il riferimento al servizio di configurazione](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="creating-data-transfer-objects"></a>La creazione degli oggetti di trasferimento di dati

Applicazione di esempio utilizza la `TodoItem` classe ai dati del modello. Per archiviare un `TodoItem` elemento nel servizio web è necessario innanzitutto convertirlo per il proxy generato `TodoItem` tipo. Questa operazione viene eseguita la `ToWCFServiceTodoItem` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Questo metodo crea un nuovo semplicemente `TodoWCFService.TodoItem` istanza e imposta la proprietà identica da ogni proprietà di `TodoItem` istanza.

Analogamente, quando i dati vengono recuperati dal servizio web, è necessario convertirlo dal proxy generato `TodoItem` tipo un `TodoItem` istanza. Questa operazione viene eseguita con il `FromWCFServiceTodoItem` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Questo metodo recupera semplicemente i dati dal proxy generato `TodoItem` tipo e lo imposta appena creato `TodoItem` istanza.

### <a name="retrieving-data"></a>Recupero dei dati

Il `TodoServiceClient.BeginGetTodoItems` e `TodoServiceClient.EndGetTodoItems` vengono utilizzati metodi per chiamare il `GetTodoItems` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoServiceClient.EndGetTodoItems` metodo una volta il `TodoServiceClient.BeginGetTodoItems` metodo viene completato, con il `null` che indica che nessun dato è stato passato nel parametro di `BeginGetTodoItems` delegato. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il `TodoServiceClient.EndGetTodoItems` metodo restituisce un `ObservableCollection` di `TodoWCFService.TodoItem` istanze, che viene quindi convertito in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="creating-data"></a>Creazione di dati

Il `TodoServiceClient.BeginCreateTodoItem` e `TodoServiceClient.EndCreateTodoItem` vengono utilizzati metodi per chiamare il `CreateTodoItem` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoServiceClient.EndCreateTodoItem` metodo una volta il `TodoServiceClient.BeginCreateTodoItem` metodo viene completato, con la `todoItem` i dati che viene passati nel parametro di `BeginCreateTodoItem` delegato per specificare il `TodoItem` da creare dal servizio web. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il servizio web genera un'eccezione un `FaultException` se risulta impossibile creare il `TodoItem`, che è gestita dall'applicazione.

### <a name="updating-data"></a>Aggiornamento di dati

Il `TodoServiceClient.BeginEditTodoItem` e `TodoServiceClient.EndEditTodoItem` vengono utilizzati metodi per chiamare il `EditTodoItem` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoServiceClient.EndEditTodoItem` metodo una volta il `TodoServiceClient.BeginCreateTodoItem` metodo viene completato, con la `todoItem` i dati che viene passati nel parametro di `BeginEditTodoItem` delegato per specificare il `TodoItem` da aggiornare tramite il servizio web. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il servizio web genera un'eccezione un `FaultException` se non è possibile individuare o aggiornare il `TodoItem`, che è gestita dall'applicazione.

### <a name="deleting-data"></a>Eliminazione di dati

Il `TodoServiceClient.BeginDeleteTodoItem` e `TodoServiceClient.EndDeleteTodoItem` vengono utilizzati metodi per chiamare il `DeleteTodoItem` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoServiceClient.EndDeleteTodoItem` metodo una volta il `TodoServiceClient.BeginDeleteTodoItem` metodo viene completato, con la `id` i dati che viene passati nel parametro di `BeginDeleteTodoItem` delegato per specificare il `TodoItem` deve essere eliminato dal servizio web. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il servizio web genera un'eccezione un `FaultException` se risulta impossibile trovare o eliminare il `TodoItem`, che è gestita dall'applicazione.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un servizio WCF SOAP da un'applicazione di xamarin. Forms. In generale, sulla piattaforma Xamarin supporta lo stesso subset di sul lato client di WCF che viene fornito con il runtime Silverlight. Ciò include le implementazioni più comuni di codifica e protocollo di WCF, ovvero codificata in formato testo di messaggi SOAP su HTTP tramite protocollo di trasporto di `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso di strumenti disponibili solo in un ambiente di Windows per generare il proxy.


## <a name="related-links"></a>Collegamenti correlati

- [TodoWCF (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
