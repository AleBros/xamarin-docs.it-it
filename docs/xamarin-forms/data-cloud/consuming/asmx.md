---
title: Utilizzo di un servizio Web ASP.NET (ASMX)
description: Questo articolo illustra come usare un servizio ASMX SOAP da un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1fa2fee36619a2a443d84b861b2473a1f616ed0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055141"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Utilizzo di un servizio Web ASP.NET (ASMX)

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX offre la possibilità di compilare servizi web che inviano messaggi usando la SOAP Simple Object Access Protocol (). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP. Questo articolo illustra come usare un servizio ASMX SOAP da un'applicazione xamarin. Forms._

Un messaggio SOAP è un documento XML che contiene gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se il *intestazione* è presente l'elemento deve essere il primo elemento figlio delle *busta* elemento.
- Un necessaria *corpo* elemento che contiene il messaggio SOAP deve essere il destinatario.
- Facoltativo *Fault* elemento che viene usato per indicare i messaggi di errore. Se il *Fault* l'elemento è presente, deve essere un elemento figlio delle *corpo* elemento.

SOAP possono agire su molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. La piattaforma Xamarin supporta implementazioni SOAP 1.1 standard su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

Istruzioni sulla configurazione del servizio ASMX possono essere trovate nel file Leggimi che accompagna l'applicazione di esempio. Tuttavia, quando viene eseguita l'applicazione di esempio, si connetterà a un servizio ASMX ospitato Xamarin che fornisce accesso in lettura ai dati, come illustrato nello screenshot seguente:

![](asmx-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio ASMX fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività|Una stringa XML serializzata TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Una stringa XML serializzata TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Una stringa XML serializzata TodoItem|

Per altre informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> L'applicazione di esempio Usa il servizio di Xamarin ospitate ASMX che fornisce accesso in lettura al servizio web. Di conseguenza, le operazioni che creano, aggiornano ed eliminare dati non modificheranno i dati usati nell'applicazione. Tuttavia, è disponibile in una versione eseguibile del servizio ASMX il **TodoASMXService** cartella nell'applicazione di esempio associato. Questa versione eseguibile dei permessi di servizio ASMX completi creare, aggiornare, leggere ed eliminare l'accesso ai dati.

Oggetto *proxy* devono essere generati per utilizzare il servizio ASMX, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito dall'utilizzo dei metadati del servizio che definisce i metodi e la configurazione del servizio associato. Questi metadati viene esposta sotto forma di un documento di servizi Web (WSDL, Web Services Description Language) generato dal servizio web. Il proxy viene compilato mediante l'aggiunta di un riferimento web per il servizio web per i progetti specifici della piattaforma.

Le classi proxy generate forniscono metodi per l'utilizzo del servizio web che usano il modello di progettazione modello di programmazione asincrono (APM). In questo modello di un'operazione asincrona viene implementata come due metodi denominati *Beginnomeoperazione* e *Endnomeoperazione*, che avviano e terminano l'operazione asincrona.

Il *Beginnomeoperazione* metodo avvia l'operazione asincrona e restituisce un oggetto che implementa il `IAsyncResult` interfaccia. Dopo avere chiamato *Beginnomeoperazione*, un'applicazione può continuare a eseguire istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un pool di thread.

Per ogni chiamata a *Beginnomeoperazione*, l'applicazione deve anche chiamare *Endnomeoperazione* per ottenere i risultati dell'operazione. Il valore restituito di *Endnomeoperazione* è dello stesso tipo restituito dal metodo del servizio web sincrono. Ad esempio, il `EndGetTodoItems` metodo restituisce una raccolta di `TodoItem` istanze. Il *Endnomeoperazione* metodo include anche un' `IAsyncResult` che deve essere impostato per l'istanza restituita dalla chiamata corrispondente al parametro il *Beginnomeoperazione* (metodo).

Task Parallel Library (TPL) può semplificare il processo di uso di una coppia di metodi begin/end APM incapsulando le operazioni asincrone nella stessa `Task` oggetto. Questo tipo di incapsulamento è fornito da più overload del `TaskFactory.FromAsync` (metodo).

Per altre informazioni su Application Performance Monitoring, vedere [modello di programmazione asincrono](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e tradizionale programmazione asincrona .NET Framework](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

### <a name="creating-the-todoservice-object"></a>Creazione dell'oggetto TodoService

Fornisce la classe proxy generata la `TodoService` (classe), che consente di comunicare con il servizio ASMX su HTTP. Fornisce funzionalità per richiamare i metodi del servizio web come operazioni asincrone da un URI identificato l'istanza del servizio. Per altre informazioni sulle operazioni asincrone, vedere [Panoramica del supporto asincrono](~/cross-platform/platform/async.md).

Il `TodoService` istanza viene dichiarata a livello di classe in modo che l'oggetto dura per fino a quando l'applicazione deve usare il servizio ASMX, come illustrato nell'esempio di codice seguente:

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

Il `TodoService` costruttore accetta un parametro di stringa facoltativo che specifica l'URL dell'istanza del servizio ASMX. In questo modo l'applicazione per connettersi a istanze diverse del servizio ASMX, condizione che sono presenti più istanze pubblicate.

### <a name="creating-data-transfer-objects"></a>La creazione degli oggetti di trasferimento dei dati

L'applicazione di esempio Usa il `TodoItem` classe ai dati del modello. Per archiviare una `TodoItem` elemento nel servizio web è necessario prima convertirlo in proxy generato `TodoItem` tipo. Questa operazione viene eseguita la `ToASMXServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

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

Questo metodo crea semplicemente una nuova `ASMService.TodoItem` dell'istanza e imposta ogni proprietà per la proprietà identica dal `TodoItem` istanza.

Analogamente, quando i dati vengono recuperati dal servizio web, è necessario convertirlo dal proxy generato `TodoItem` tipo di un `TodoItem` istanza. Questa operazione viene eseguita con il `FromASMXServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

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

Il `TodoService.BeginGetTodoItems` e `TodoService.EndGetTodoItems` vengono utilizzati metodi per chiamare il `GetTodoItems` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un `Task` che esegue il `TodoService.EndGetTodoItems` metodo una volta il `TodoService.BeginGetTodoItems` metodo viene completato, con il `null` parametro che indica che nessun dato vengono passato nel `BeginGetTodoItems` delegare. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il `TodoService.EndGetTodoItems` metodo restituisce una matrice di `ASMXService.TodoItem` istanze, che viene quindi convertito in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="creating-data"></a>Creazione di dati

Il `TodoService.BeginCreateTodoItem` e `TodoService.EndCreateTodoItem` vengono utilizzati metodi per chiamare il `CreateTodoItem` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un' `Task` che esegue il `TodoService.EndCreateTodoItem` metodo una volta il `TodoService.BeginCreateTodoItem` metodo viene completato, con il `todoItem` in corso i dati che viene passati nel parametro il `BeginCreateTodoItem` delegato per specificare il `TodoItem` deve essere creato dal servizio web. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il servizio web genera un'eccezione una `SoapException` se non riesce a creare il `TodoItem`, che viene gestita dall'applicazione.

### <a name="updating-data"></a>Aggiornamento di dati

Il `TodoService.BeginEditTodoItem` e `TodoService.EndEditTodoItem` vengono utilizzati metodi per chiamare il `EditTodoItem` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un' `Task` che esegue il `TodoService.EndEditTodoItem` metodo una volta il `TodoService.BeginCreateTodoItem` metodo viene completato, con il `todoItem` in corso i dati che viene passati nel parametro il `BeginEditTodoItem` delegato per specificare il `TodoItem` da aggiornare tramite il servizio web. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il servizio web genera un'eccezione una `SoapException` se non riesce a individuare o aggiornare il `TodoItem`, che viene gestita dall'applicazione.

### <a name="deleting-data"></a>Eliminazione di dati

Il `TodoService.BeginDeleteTodoItem` e `TodoService.EndDeleteTodoItem` vengono utilizzati metodi per chiamare il `DeleteTodoItem` operazione fornita dal servizio web. Questi metodi asincroni sono incapsulati in un `Task` dell'oggetto, come illustrato nell'esempio di codice seguente:

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

Il `Task.Factory.FromAsync` metodo crea un' `Task` che esegue il `TodoService.EndDeleteTodoItem` metodo una volta il `TodoService.BeginDeleteTodoItem` metodo viene completato, con il `id` in corso i dati che viene passati nel parametro il `BeginDeleteTodoItem` delegato per specificare il `TodoItem` deve essere eliminato dal servizio web. Infine, il valore della `TaskCreationOptions` enumerazione specifica che deve essere utilizzato il comportamento predefinito per la creazione ed esecuzione di attività.

Il servizio web genera un'eccezione una `SoapException` se non riesce a trovare o eliminare il `TodoItem`, che viene gestita dall'applicazione.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare un servizio web ASMX da un'applicazione xamarin. Forms. ASMX offre la possibilità di compilare servizi web che inviano messaggi su HTTP utilizzando SOAP. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP.


## <a name="related-links"></a>Collegamenti correlati

- [TodoASMX (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
