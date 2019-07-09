---
title: Usare un servizio Web ASP.NET (ASMX)
description: Questo articolo illustra come usare un servizio ASMX SOAP da un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 73e7d13188c4c2ea1be39a237c3dc5cfd7fd8a7d
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659058"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Usare un servizio Web ASP.NET (ASMX)

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX offre la possibilità di compilare servizi web che inviano messaggi usando la SOAP Simple Object Access Protocol (). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP. Questo articolo illustra come usare un servizio ASMX SOAP da un'applicazione xamarin. Forms._

Un messaggio SOAP è un documento XML che contiene gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se il *intestazione* è presente l'elemento deve essere il primo elemento figlio delle *busta* elemento.
- Un necessaria *corpo* elemento che contiene il messaggio SOAP deve essere il destinatario.
- Facoltativo *Fault* elemento che viene usato per indicare i messaggi di errore. Se il *Fault* l'elemento è presente, deve essere un elemento figlio delle *corpo* elemento.

SOAP possono agire su molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. La piattaforma Xamarin supporta implementazioni SOAP 1.1 standard su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

In questo esempio include le applicazioni per dispositivi mobili in esecuzione su dispositivi fisici o emulati e un servizio ASMX che fornisce metodi per ottenere, aggiungere, modificare ed eliminare dati. Quando vengono eseguite le applicazioni per dispositivi mobili, si connettono al servizio ASMX ospitate in locale come illustrato nello screenshot seguente:

![](asmx-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Utilizzare il servizio web

Il servizio ASMX fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività|Una stringa XML serializzata TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Una stringa XML serializzata TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Una stringa XML serializzata TodoItem|

Per altre informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Creare il proxy TodoService

Una classe proxy, chiamata `TodoService`, si estende `SoapHttpClientProtocol` e fornisce metodi per comunicare con il servizio ASMX tramite HTTP. Il proxy viene generato mediante l'aggiunta di un riferimento web a ogni progetto specifico della piattaforma in Visual Studio 2017 o Visual Studio 2019. Il riferimento web genera metodi ed eventi per ogni azione definita nel documento Web Services Description Language (WSDL) del servizio.

Ad esempio, il `GetTodoItems` azione del servizio comporta un `GetTodoItemsAsync` (metodo) e un `GetTodoItemsCompleted` evento nel proxy. Il metodo generato ha un tipo restituito void e richiama il `GetTodoItems` azione nell'elemento padre `SoapHttpClientProtocol` classe. Quando il metodo richiamato riceve una risposta dal servizio, che genera la `GetTodoItemsCompleted` eventi e fornisce i dati di risposta all'interno dell'evento `Result` proprietà.

## <a name="create-the-isoapservice-implementation"></a>Creare l'implementazione ISoapService

Per abilitare il progetto condiviso, lo sviluppo multipiattaforma lavorare con il servizio, l'esempio definisce i `ISoapService` dell'interfaccia, che segue il [modello di programmazione asincrona attività in C# ](/dotnet/csharp/programming-guide/concepts/async/). Ogni piattaforma implementa il `ISoapService` per esporre il proxy specifico della piattaforma. L'esempio Usa `TaskCompletionSource` oggetti per esporre il proxy come un'interfaccia asincrona di attività. Informazioni dettagliate sull'uso `TaskCompletionSource` si trovano nelle implementazioni di tipi di azioni nelle sezioni seguenti.

L'esempio `SoapService`:

1. Crea un'istanza di `TodoService` come un'istanza a livello di classe
1. Crea una raccolta denominata `Items` archiviare `TodoItem` oggetti
1. Specifica un endpoint personalizzato per l'opzione facoltativa `Url` proprietà di `TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>Creare gli oggetti di trasferimento dei dati

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

Questo metodo crea un nuovo `ASMService.TodoItem` dell'istanza e imposta ogni proprietà per la proprietà identica dal `TodoItem` istanza.

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

Questo metodo recupera i dati dal proxy generato `TodoItem` tipo e lo imposta appena creato `TodoItem` istanza.

### <a name="retrieve-data"></a>Recupero dei dati

Il `ISoapService` interfaccia prevede il `RefreshDataAsync` metodo restituisca un `Task` con la raccolta di elementi. Tuttavia, il `TodoService.GetTodoItemsAsync` metodo restituisce void. Per soddisfare il modello di interfaccia, è necessario chiamare `GetTodoItemsAsync`, attendere il `GetTodoItemsCompleted` eventi vengono attivati e popolare la raccolta. In questo modo è possibile restituire una raccolta valida per l'interfaccia utente.

L'esempio seguente crea una nuova `TaskCompletionSource`, inizia la chiamata asincrona nel `RefreshDataAsync` metodo e attende la `Task` forniti dal `TaskCompletionSource`. Quando la `TodoService_GetTodoItemsCompleted` viene richiamato il gestore di evento popola la `Items` raccolta e gli aggiornamenti di `TaskCompletionSource`:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

Per altre informazioni, vedere [modello di programmazione asincrono](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) e [TPL e tradizionale programmazione asincrona .NET Framework](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Creare o modificare i dati

Quando si crea o modifica dei dati, è necessario implementare il `ISoapService.SaveTodoItemAsync` (metodo). Questo metodo consente di rilevare se il `TodoItem` è un elemento nuovo o aggiornato e chiama il metodo appropriato nel `todoService` oggetto. Il `CreateTodoItemCompleted` e `EditTodoItemCompleted` gestori eventi devono inoltre essere implementati in modo da sapere quando il `todoService` ha ricevuto una risposta dal servizio ASMX (questi possono essere combinati in un singolo gestore poiché eseguono la stessa operazione). L'esempio seguente illustra le implementazioni del gestore evento e dell'interfaccia, così come il `TaskCompletionSource` oggetto utilizzato per eseguire operazioni asincrone:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>Eliminare i dati

L'eliminazione dei dati richiede un'implementazione simile. Definire un `TaskCompletionSource`, implementare un gestore eventi e il `ISoapService.DeleteTodoItemAsync` metodo:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>Testare il servizio web

Test dispositivi fisici o emulati con un servizio ospitato in locale richiede la configurazione personalizzata di IIS, gli indirizzi degli endpoint e le regole del firewall per essere disponibili. Per altre informazioni su come configurare l'ambiente di test, vedere la [configurare l'accesso remoto a IIS Express](wcf.md#configure-remote-access-to-iis-express). L'unica differenza tra il test ASMX e WCF è il numero di porta del TodoService.

## <a name="related-links"></a>Collegamenti correlati

- [TodoASMX (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
