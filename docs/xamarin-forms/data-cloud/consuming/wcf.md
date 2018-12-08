---
title: Utilizzo di un servizio Web di Windows Communication Foundation (WCF)
description: Questo articolo illustra come usare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 7e8acc6e8aaf8b8e0e8cec7d5d0f3e28cf60073a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055595"
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>Utilizzo di un servizio Web di Windows Communication Foundation (WCF)

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_WCF è un framework unificato di Microsoft per la compilazione di applicazioni orientate ai servizi. Consente agli sviluppatori di compilare applicazioni distribuite sicure, affidabili, transazionali e interoperabile. Questo articolo illustra come usare un servizio WCF SOAP Simple Object Access Protocol () da un'applicazione xamarin. Forms._

WCF descrive un servizio con un'ampia gamma di contratti diversi che includono i seguenti:

- **I contratti dati** : definire le strutture di dati che costituiscono la base per il contenuto all'interno di un messaggio.
- **Contratti di messaggio** : comporre i messaggi da contratti dati esistenti.
- **I contratti di errore** -Consenti errori SOAP personalizzati essere specificato.
- **I contratti di servizio** : specificare le operazioni che supportano servizi e i messaggi necessari per l'interazione con ogni operazione. Specificano anche eventuali comportamenti di errore personalizzato che possono essere associato a operazioni su ogni servizio.

Esistono differenze tra servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità offerte da ASMX: messaggi SOAP su HTTP. Per altre informazioni sull'utilizzo di un servizio ASMX, vedere [utilizzo di servizi Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

In generale, la piattaforma Xamarin supporta lo stesso subset di lato client di WCF, che viene fornito con il runtime di Silverlight. Ciò include le implementazioni più comuni di codifica e il protocollo di WCF, ovvero codificata in formato testo di messaggi SOAP su HTTP il trasporto di protocollo tramite la `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso degli strumenti disponibili solo in un ambiente di Windows per generare il proxy.

Istruzioni sulla configurazione del servizio WCF sono reperibile nel file Leggimi che accompagna l'applicazione di esempio. Tuttavia, quando viene eseguita l'applicazione di esempio si connetterà a un servizio WCF ospitato in Xamarin che fornisce accesso in lettura ai dati, come illustrato nello screenshot seguente:

![](wcf-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio WCF fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività|Una stringa XML serializzata TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Una stringa XML serializzata TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Una stringa XML serializzata TodoItem|

Per altre informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> L'applicazione di esempio Usa il servizio WCF ospitato in Xamarin che fornisce accesso in lettura al servizio web. Di conseguenza, le operazioni che creano, aggiornano ed eliminare dati non modificheranno i dati usati nell'applicazione. Tuttavia, è disponibile in una versione eseguibile del servizio ASMX il **TodoWCFService** cartella nell'applicazione di esempio associato. Questa versione hostable dei permessi di servizio di WCF completi di creare, aggiornare, leggere ed eliminare l'accesso ai dati.

Oggetto *proxy* deve essere generato per l'utilizzo di un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definiscono i metodi e la configurazione del servizio associato. Questi metadati viene esposta sotto forma di un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy può essere compilato usando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio web a una libreria .NET Standard. Un'alternativa alla creazione del proxy utilizzando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 è usare la strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per altre informazioni, vedere [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Le classi proxy generate forniscono metodi per l'uso dei servizi web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Ad esempio, il `EndGetTodoItems` metodo restituisce una raccolta di `TodoItem` istanze. Il *Endnomeoperazione* metodo include anche un' `IAsyncResult` che deve essere impostato per l'istanza restituita dalla chiamata corrispondente al parametro il *Beginnomeoperazione* (metodo).

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `TaskFactory.FromAsync` (metodo).

Per altre informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e tradizionale programmazione asincrona .NET Framework](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

### <a name="creating-the-todoserviceclient-object"></a>Creazione dell'oggetto TodoServiceClient

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

### <a name="creating-data-transfer-objects"></a>La creazione degli oggetti di trasferimento dei dati

L'applicazione di esempio Usa il `TodoItem` classe ai dati del modello. Per archiviare una `TodoItem` elemento nel servizio web è necessario prima convertirlo in proxy generato `TodoItem` tipo. Questa operazione viene eseguita la `ToWCFServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

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

Questo metodo crea semplicemente una nuova `TodoWCFService.TodoItem` dell'istanza e imposta ogni proprietà per la proprietà identica dal `TodoItem` istanza.

Analogamente, quando i dati vengono recuperati dal servizio web, è necessario convertirlo dal proxy generato `TodoItem` tipo di un `TodoItem` istanza. Questa operazione viene eseguita con il `FromWCFServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

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

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoServiceClient.EndGetTodoItems` metodo una volta il `TodoServiceClient.BeginGetTodoItems` metodo viene completato, con il `null` parametro che indica che nessun dato vengono passato nel `BeginGetTodoItems` delegare. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il `TodoServiceClient.EndGetTodoItems` metodo restituisce un `ObservableCollection` dei `TodoWCFService.TodoItem` istanze, che viene quindi convertito in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="creating-data"></a>Creazione di dati

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

### <a name="updating-data"></a>Aggiornamento di dati

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

### <a name="deleting-data"></a>Eliminazione di dati

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

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare un servizio WCF SOAP da un'applicazione xamarin. Forms. In generale, la piattaforma Xamarin supporta lo stesso subset di lato client di WCF, che viene fornito con il runtime di Silverlight. Ciò include le implementazioni più comuni di codifica e il protocollo di WCF, ovvero codificata in formato testo di messaggi SOAP su HTTP il trasporto di protocollo tramite la `BasicHttpBinding` classe. Inoltre, il supporto WCF richiede l'uso degli strumenti disponibili solo in un ambiente di Windows per generare il proxy.


## <a name="related-links"></a>Collegamenti correlati

- [TodoWCF (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
