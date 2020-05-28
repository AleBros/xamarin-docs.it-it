---
title: ''
description: In questo articolo viene illustrato come utilizzare un servizio SOAP ASMX da un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f7a0d04d1e7b6abc9931c05c0e46ef49f8ba09c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138463"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Utilizzare un servizio Web ASP.NET (ASMX)

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX consente di compilare servizi Web che inviano messaggi utilizzando il Simple Object Access Protocol (SOAP). SOAP è un protocollo indipendente dalla piattaforma e indipendente dalla lingua per la creazione e l'accesso ai servizi Web. I consumer di un servizio ASMX non devono conoscere alcuna piattaforma, modello a oggetti o linguaggio di programmazione utilizzato per implementare il servizio. È sufficiente comprendere come inviare e ricevere messaggi SOAP. In questo articolo viene illustrato come utilizzare un servizio SOAP ASMX da un' Xamarin.Forms applicazione._

Un messaggio SOAP è un documento XML contenente gli elementi seguenti:

- Elemento radice denominato *Envelope* che identifica il documento XML come messaggio SOAP.
- Elemento *intestazione* facoltativo che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se l'elemento dell' *intestazione* è presente, deve essere il primo elemento figlio dell'elemento *Envelope* .
- Elemento del *corpo* obbligatorio che contiene il messaggio SOAP destinato al destinatario.
- Elemento *fault* facoltativo utilizzato per indicare i messaggi di errore. Se l'elemento *fault* è presente, deve essere un elemento figlio dell'elemento *Body* .

SOAP può utilizzare molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può funzionare solo tramite HTTP. La piattaforma Novell supporta le implementazioni standard di SOAP 1,1 su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

Questo esempio include le applicazioni per dispositivi mobili che vengono eseguite su dispositivi fisici o emulati e un servizio ASMX che fornisce i metodi per ottenere, aggiungere, modificare ed eliminare i dati. Quando vengono eseguite, le applicazioni per dispositivi mobili si connettono al servizio ASMX ospitato localmente, come illustrato nello screenshot seguente:

![](asmx-images/portal.png "Sample Application")

> [!NOTE]
> In iOS 9 e versioni successive, la sicurezza del trasporto app impone connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo così la divulgazione accidentale di informazioni riservate. Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9, tutte le connessioni saranno soggette ai requisiti di sicurezza di ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.
> È possibile escludere ATS da se non è possibile usare il `HTTPS` protocollo e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Utilizzare il servizio Web

Il servizio ASMX fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Crea nuovo elemento attività|TodoItem serializzato XML|
|EditTodoItem|Aggiornare un elemento attività|TodoItem serializzato XML|
|DeleteTodoItem|Eliminare un elemento attività|TodoItem serializzato XML|

Per ulteriori informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Creare il proxy TodoService

Una classe proxy, denominata `TodoService` , estende `SoapHttpClientProtocol` e fornisce metodi per la comunicazione con il servizio ASMX su http. Il proxy viene generato tramite l'aggiunta di un riferimento Web a ogni progetto specifico della piattaforma in Visual Studio 2019 o Visual Studio 2017. Il riferimento Web genera metodi ed eventi per ogni azione definita nel documento di Web Services Description Language (WSDL) del servizio.

L'azione di servizio, ad esempio, `GetTodoItems` comporta un `GetTodoItemsAsync` metodo e un `GetTodoItemsCompleted` evento nel proxy. Il metodo generato ha un tipo restituito void e richiama l' `GetTodoItems` azione sulla `SoapHttpClientProtocol` classe padre. Quando il metodo richiamato riceve una risposta dal servizio, genera l' `GetTodoItemsCompleted` evento e fornisce i dati di risposta all'interno della proprietà dell'evento `Result` .

## <a name="create-the-isoapservice-implementation"></a>Creare l'implementazione di ISoapService

Per consentire al progetto multipiattaforma condiviso di funzionare con il servizio, l'esempio definisce l' `ISoapService` interfaccia che segue il [modello di programmazione asincrona delle attività in C#](/dotnet/csharp/programming-guide/concepts/async/). Ogni piattaforma implementa `ISoapService` per esporre il proxy specifico della piattaforma. Nell'esempio vengono utilizzati `TaskCompletionSource` oggetti per esporre il proxy come interfaccia asincrona dell'attività. I dettagli sull'uso di `TaskCompletionSource` si trovano nelle implementazioni di ogni tipo di azione nelle sezioni riportate di seguito.

Esempio `SoapService` :

1. Crea un' `TodoService` istanza di come istanza a livello di classe
1. Crea una raccolta chiamata `Items` per archiviare `TodoItem` oggetti.
1. Specifica un endpoint personalizzato per la proprietà facoltativa `Url` nel parametro`TodoService`

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

### <a name="create-data-transfer-objects"></a>Creare oggetti di trasferimento dati

L'applicazione di esempio usa la `TodoItem` classe per modellare i dati. Per archiviare un `TodoItem` elemento nel servizio Web, è necessario prima convertirlo nel tipo generato dal proxy `TodoItem` . Questa operazione viene eseguita dal `ToASMXServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

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

Questo metodo crea una nuova `ASMService.TodoItem` istanza di e imposta ogni proprietà sulla proprietà identica dall'istanza di `TodoItem` .

Analogamente, quando i dati vengono recuperati dal servizio Web, è necessario convertirli dal tipo generato dal proxy `TodoItem` a un' `TodoItem` istanza di. Questa operazione viene eseguita con il `FromASMXServiceTodoItem` metodo, come illustrato nell'esempio di codice seguente:

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

Questo metodo recupera i dati dal tipo generato dal proxy `TodoItem` e li imposta nell'istanza appena creata `TodoItem` .

### <a name="retrieve-data"></a>Recuperare i dati

L' `ISoapService` interfaccia prevede che il `RefreshDataAsync` metodo restituisca un oggetto `Task` con la raccolta di elementi. Tuttavia, il `TodoService.GetTodoItemsAsync` metodo restituisce void. Per soddisfare il modello di interfaccia, è necessario chiamare `GetTodoItemsAsync` , attendere che l' `GetTodoItemsCompleted` evento venga attivato e popolare la raccolta. In questo modo è possibile restituire una raccolta valida all'interfaccia utente.

Nell'esempio seguente viene creato un nuovo oggetto `TaskCompletionSource` , viene avviata la chiamata asincrona nel `RefreshDataAsync` metodo e viene atteso l'oggetto `Task` fornito da `TaskCompletionSource` . Quando `TodoService_GetTodoItemsCompleted` viene richiamato il gestore eventi, compila la `Items` raccolta e aggiorna `TaskCompletionSource` :

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

Per altre informazioni, vedere [modello di programmazione asincrona](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) e [TPL e tradizionale .NET Framework la programmazione asincrona](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Creazione o modifica di dati

Quando si creano o si modificano i dati, è necessario implementare il `ISoapService.SaveTodoItemAsync` metodo. Questo metodo rileva se `TodoItem` è un elemento nuovo o aggiornato e chiama il metodo appropriato per l' `todoService` oggetto. `CreateTodoItemCompleted`Anche i `EditTodoItemCompleted` gestori di eventi e devono essere implementati in modo da stabilire quando `todoService` ha ricevuto una risposta dal servizio ASMX (questi possono essere combinati in un singolo gestore perché eseguono la stessa operazione). Nell'esempio seguente vengono illustrate le implementazioni dell'interfaccia e del gestore eventi, nonché l' `TaskCompletionSource` oggetto utilizzato per operare in modo asincrono:

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

L'eliminazione dei dati richiede un'implementazione simile. Definire un oggetto `TaskCompletionSource` , implementare un gestore eventi e il `ISoapService.DeleteTodoItemAsync` Metodo:

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

## <a name="test-the-web-service"></a>Testare il servizio Web

Il test di dispositivi fisici o emulati con un servizio ospitato localmente richiede la configurazione personalizzata di IIS, gli indirizzi degli endpoint e le regole del firewall. Per informazioni più dettagliate su come configurare l'ambiente per il test, vedere la pagina relativa alla [configurazione dell'accesso remoto per IIS Express](wcf.md#configure-remote-access-to-iis-express). L'unica differenza tra il testing di WCF e ASMX è il numero di porta di TodoService.

## <a name="related-links"></a>Collegamenti correlati

- [TodoASMX (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
