---
title: Utilizzo di un servizio Web ASP.NET (ASMX)
description: In questo articolo viene illustrato come utilizzare un servizio ASMX SOAP da un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 6ec8168a8da64dbf3dfeb805856a4d91c9ec78ca
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242063"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Utilizzo di un servizio Web ASP.NET (ASMX)

_ASMX offre la possibilità di compilare servizi web che inviano messaggi utilizzando l'oggetto accesso protocollo SOAP (Simple). SOAP è un protocollo indipendente dalla piattaforma e indipendente dal linguaggio per la compilazione e l'accesso ai servizi web. I consumer di un servizio ASMX non è necessario conoscere la piattaforma, un modello a oggetti o un linguaggio di programmazione utilizzato per implementare il servizio. È sufficiente comprendere come inviare e ricevere messaggi SOAP. In questo articolo viene illustrato come utilizzare un servizio ASMX SOAP da un'applicazione di xamarin. Forms._

Un messaggio SOAP è un documento XML contenente gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come un messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio dati di autenticazione. Se il *intestazione* elemento è presente deve essere il primo elemento figlio del *busta* elemento.
- Richiesta *corpo* elemento che contiene il messaggio SOAP per il destinatario.
- Facoltativo *errore* elemento che viene utilizzato per indicare i messaggi di errore. Se il *errore* elemento è presente, deve essere un elemento figlio del *corpo* elemento.

SOAP possono operare su numerosi protocolli di trasporto, come HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. Sulla piattaforma Xamarin supporta implementazioni di SOAP 1.1 standard su HTTP, e questo include il supporto per molte delle configurazioni del servizio ASMX standard.

Istruzioni sull'impostazione di servizio ASMX sono reperibile nel file Leggimi che accompagna l'applicazione di esempio. Tuttavia, quando viene eseguita l'applicazione di esempio, la si connetterà a un servizio ospitato Xamarin ASMX che fornisce l'accesso in sola lettura ai dati, come illustrato nella schermata seguente:

![](asmx-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio ASMX fornisce le seguenti operazioni:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività da eseguire|Un codice XML serializzato TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Un codice XML serializzato TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Un codice XML serializzato TodoItem|

Per ulteriori informazioni sul modello di data utilizzato nell'applicazione, vedere [modellazione dati](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> L'applicazione di esempio utilizza il servizio ospitato Xamarin ASMX che fornisce l'accesso in sola lettura per il servizio web. Le operazioni di creano, aggiornano ed eliminare dati, pertanto, non eliminerà i dati utilizzati nell'applicazione. È tuttavia disponibile in una versione eseguibile del servizio ASMX il **TodoASMXService** cartella nell'applicazione di esempio associato. Questa versione di pesca servizio ASMX completo eseguibile creare, aggiornare, leggere ed elimina l'accesso ai dati.

Oggetto *proxy* deve essere generato per utilizzare il servizio ASMX, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito mediante dispendiosa in termini di metadati del servizio che definisce i metodi e la configurazione del servizio associato. I metadati vengono esposti nel formato di un documento Web Services Description Language (WSDL) che viene generato dal servizio web. Il proxy viene compilato mediante l'aggiunta di un riferimento web per il servizio web per i progetti specifici della piattaforma.

Le classi proxy generato forniscono metodi per l'utilizzo del servizio web che utilizzano il modello di progettazione del modello di programmazione asincrono (APM). In questo modello di un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il *BeginOperationName* metodo inizia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo la chiamata *BeginOperationName*, un'applicazione può continuare l'esecuzione di istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve chiamare anche *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo del servizio web sincrono. Ad esempio, il `EndGetTodoItems` il metodo restituisce una raccolta di `TodoItem` istanze. Il *EndOperationName* metodo include anche un `IAsyncResult` parametro che deve essere impostato per l'istanza restituita dalla chiamata corrispondente al *BeginOperationName* metodo.

Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nello stesso `Task` oggetto. Fornito da più overload di questo incapsulamento di `TaskFactory.FromAsync` metodo.

Per ulteriori informazioni su APM vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e .NET Framework programmazione asincrona tradizionale](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

### <a name="creating-the-todoservice-object"></a>Creazione dell'oggetto TodoService

Fornisce la classe proxy generata la `TodoService` (classe), che viene utilizzato per comunicare con il servizio ASMX su HTTP. Fornisce funzionalità per richiamare i metodi del servizio web come operazioni asincrone da un URI identificato l'istanza del servizio. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto di Async](~/cross-platform/platform/async.md).

Il `TodoService` istanza viene dichiarata a livello di classe in modo che l'oggetto ha una validità fino a quando l'applicazione deve utilizzare il servizio ASMX, come illustrato nell'esempio di codice seguente:

```csharp
public class SoapService : ISoapService
{
  ASMXService.TodoService asmxService;
  ...

  public SoapService ()
  {
    asmxService = new ASMXService.TodoService (Constants.SoapUrl);
  }
  ...
}
```

Il `TodoService` costruttore accetta un parametro di stringa facoltativo che specifica l'URL dell'istanza del servizio ASMX. In questo modo l'applicazione per la connessione tra le diverse istanze del servizio ASMX, purché non ci siano più istanze pubblicate.

### <a name="creating-data-transfer-objects"></a>La creazione degli oggetti di trasferimento di dati

Applicazione di esempio utilizza la `TodoItem` classe ai dati del modello. Per archiviare un `TodoItem` elemento nel servizio web è necessario innanzitutto convertirlo per il proxy generato `TodoItem` tipo. Questa operazione viene eseguita la `ToASMXServiceTodoItem` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
  return new ASMXService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Questo metodo crea un nuovo semplicemente `ASMService.TodoItem` istanza e imposta la proprietà identica da ogni proprietà di `TodoItem` istanza.

Analogamente, quando i dati vengono recuperati dal servizio web, è necessario convertirlo dal proxy generato `TodoItem` tipo un `TodoItem` istanza. Questa operazione viene eseguita con il `FromASMXServiceTodoItem` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
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

Il `TodoService.BeginGetTodoItems` e `TodoService.EndGetTodoItems` vengono utilizzati metodi per chiamare il `GetTodoItems` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromASMXServiceTodoItem (item));
  }
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoService.EndGetTodoItems` metodo una volta il `TodoService.BeginGetTodoItems` metodo viene completato, con il `null` che indica che nessun dato è stato passato nel parametro di `BeginGetTodoItems` delegato. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il `TodoService.EndGetTodoItems` il metodo restituisce una matrice di `ASMXService.TodoItem` istanze, che viene quindi convertito in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="creating-data"></a>Creazione di dati

Il `TodoService.BeginCreateTodoItem` e `TodoService.EndCreateTodoItem` vengono utilizzati metodi per chiamare il `CreateTodoItem` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoService.EndCreateTodoItem` metodo una volta il `TodoService.BeginCreateTodoItem` metodo viene completato, con la `todoItem` i dati che viene passati nel parametro di `BeginCreateTodoItem` delegato per specificare il `TodoItem` da creare dal servizio web. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il servizio web genera un'eccezione un `SoapException` se risulta impossibile creare il `TodoItem`, che è gestita dall'applicazione.

### <a name="updating-data"></a>Aggiornamento di dati

Il `TodoService.BeginEditTodoItem` e `TodoService.EndEditTodoItem` vengono utilizzati metodi per chiamare il `EditTodoItem` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoService.EndEditTodoItem` metodo una volta il `TodoService.BeginCreateTodoItem` metodo viene completato, con la `todoItem` i dati che viene passati nel parametro di `BeginEditTodoItem` delegato per specificare il `TodoItem` da aggiornare tramite il servizio web. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il servizio web genera un'eccezione un `SoapException` se non è possibile individuare o aggiornare il `TodoItem`, che è gestita dall'applicazione.

### <a name="deleting-data"></a>Eliminazione di dati

Il `TodoService.BeginDeleteTodoItem` e `TodoService.EndDeleteTodoItem` vengono utilizzati metodi per chiamare il `DeleteTodoItem` operazione fornita dal servizio web. Questi metodi asincroni vengono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoService.EndDeleteTodoItem` metodo una volta il `TodoService.BeginDeleteTodoItem` metodo viene completato, con la `id` i dati che viene passati nel parametro di `BeginDeleteTodoItem` delegato per specificare il `TodoItem` deve essere eliminato dal servizio web. Infine, il valore di `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione di attività.

Il servizio web genera un'eccezione un `SoapException` se risulta impossibile trovare o eliminare il `TodoItem`, che è gestita dall'applicazione.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un servizio web ASMX da un'applicazione di xamarin. Forms. ASMX offre la possibilità di compilare servizi web che inviano messaggi su HTTP tramite SOAP. I consumer di un servizio ASMX non è necessario conoscere la piattaforma, un modello a oggetti o un linguaggio di programmazione utilizzato per implementare il servizio. È sufficiente comprendere come inviare e ricevere messaggi SOAP.


## <a name="related-links"></a>Collegamenti correlati

- [TodoASMX (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
