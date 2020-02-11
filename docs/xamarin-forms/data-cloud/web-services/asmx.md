---
title: Utilizzare un servizio Web ASP.NET (ASMX)
description: Questo articolo illustra come usare un servizio ASMX SOAP da un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 124cffaf827ebeb8109f3cd4ac4dfd2701e43f9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656650"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Utilizzare un servizio Web ASP.NET (ASMX)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX offre la possibilità di compilare servizi web che inviano messaggi usando la SOAP Simple Object Access Protocol (). SOAP è un protocollo indipendente dalla piattaforma e indipendenti dal linguaggio per la creazione e accesso ai servizi web. I consumer di un servizio ASMX non sono necessario conoscere il linguaggio sul modello a oggetti, piattaforma o linguaggio di programmazione usato per implementare il servizio. Devono solo per informazioni su come inviare e ricevere messaggi SOAP. Questo articolo illustra come usare un servizio ASMX SOAP da un'applicazione Xamarin.Forms._

Un messaggio SOAP è un documento XML che contiene gli elementi seguenti:

- Un elemento radice denominato *busta* che identifica il documento XML come messaggio SOAP.
- Facoltativo *intestazione* elemento che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se il *intestazione* è presente l'elemento deve essere il primo elemento figlio delle *busta* elemento.
- Un necessaria *corpo* elemento che contiene il messaggio SOAP deve essere il destinatario.
- Facoltativo *Fault* elemento che viene usato per indicare i messaggi di errore. Se il *Fault* l'elemento è presente, deve essere un elemento figlio delle *corpo* elemento.

SOAP possono agire su molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può essere utilizzato solo su HTTP. La piattaforma Xamarin supporta implementazioni SOAP 1.1 standard su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

Questo esempio include le applicazioni per dispositivi mobili che vengono eseguite su dispositivi fisici o emulati e un servizio ASMX che fornisce i metodi per ottenere, aggiungere, modificare ed eliminare i dati. Quando vengono eseguite, le applicazioni per dispositivi mobili si connettono al servizio ASMX ospitato localmente, come illustrato nello screenshot seguente:

![](asmx-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Utilizzare il servizio Web

Il servizio ASMX fornisce le operazioni seguenti:

|Operazione|Descrizione|Parametri|
|--- |--- |--- |
|GetTodoItems|Ottenere un elenco di elementi attività|
|CreateTodoItem|Creare un nuovo elemento di attività|Una stringa XML serializzata TodoItem|
|EditTodoItem|Aggiornare un elemento attività|Una stringa XML serializzata TodoItem|
|DeleteTodoItem|Eliminare un elemento attività|Una stringa XML serializzata TodoItem|

Per altre informazioni sul modello di dati utilizzato nell'applicazione, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Creare il proxy TodoService

Una classe proxy, denominata `TodoService`, estende `SoapHttpClientProtocol` e fornisce metodi per la comunicazione con il servizio ASMX su http. Il proxy viene generato tramite l'aggiunta di un riferimento Web a ogni progetto specifico della piattaforma in Visual Studio 2019 o Visual Studio 2017. Il riferimento Web genera metodi ed eventi per ogni azione definita nel documento di Web Services Description Language (WSDL) del servizio.

L' `GetTodoItems` azione di servizio, ad esempio, comporta `GetTodoItemsAsync` un metodo e `GetTodoItemsCompleted` un evento nel proxy. Il metodo generato ha un tipo restituito void e richiama l' `GetTodoItems` azione sulla classe padre. `SoapHttpClientProtocol` Quando il metodo richiamato riceve una risposta dal servizio, genera l' `GetTodoItemsCompleted` evento e fornisce i dati di `Result` risposta all'interno della proprietà dell'evento.

## <a name="create-the-isoapservice-implementation"></a>Creare l'implementazione di ISoapService

Per consentire al progetto multipiattaforma condiviso di funzionare con il servizio, nell'esempio viene definita l' `ISoapService` interfaccia, che segue il [modello di programmazione asincrona delle attività C#in ](/dotnet/csharp/programming-guide/concepts/async/). Ogni piattaforma implementa `ISoapService` per esporre il proxy specifico della piattaforma. Nell'esempio vengono `TaskCompletionSource` utilizzati oggetti per esporre il proxy come interfaccia asincrona dell'attività. I dettagli sull' `TaskCompletionSource` uso di si trovano nelle implementazioni di ogni tipo di azione nelle sezioni riportate di seguito.

Esempio `SoapService`:

1. Crea un'istanza di come istanza a livello di classe `TodoService`
1. Crea una raccolta chiamata `Items` per archiviare `TodoItem` oggetti.
1. Specifica un endpoint personalizzato per la proprietà `Url` facoltativa nel parametro`TodoService`

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

Questo metodo crea una nuova `ASMService.TodoItem` istanza di e imposta ogni proprietà sulla proprietà identica `TodoItem` dall'istanza di.

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

Questo metodo recupera i dati dal tipo generato `TodoItem` dal proxy e li imposta nell'istanza appena creata. `TodoItem`

### <a name="retrieve-data"></a>Recuperare i dati

L' `ISoapService` interfaccia prevede che il `RefreshDataAsync` metodo restituisca un `Task` oggetto con la raccolta di elementi. Tuttavia, il `TodoService.GetTodoItemsAsync` metodo restituisce void. Per soddisfare il modello di interfaccia, è necessario `GetTodoItemsAsync`chiamare, attendere che `GetTodoItemsCompleted` l'evento venga attivato e popolare la raccolta. In questo modo è possibile restituire una raccolta valida all'interfaccia utente.

Nell'esempio seguente viene creato un `TaskCompletionSource`nuovo oggetto, viene avviata la `RefreshDataAsync` chiamata asincrona nel metodo e viene `Task` atteso l' `TaskCompletionSource`oggetto fornito da. Quando viene `TodoService_GetTodoItemsCompleted` richiamato il gestore eventi, compila la `Items` raccolta e aggiorna `TaskCompletionSource`:

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

Quando si creano o si modificano i dati, è `ISoapService.SaveTodoItemAsync` necessario implementare il metodo. Questo metodo rileva se `TodoItem` è un elemento nuovo o aggiornato e chiama il metodo appropriato per l' `todoService` oggetto. Anche `CreateTodoItemCompleted` i `EditTodoItemCompleted` gestori di eventi e devono essere implementati in `todoService` modo da stabilire quando ha ricevuto una risposta dal servizio ASMX (questi possono essere combinati in un singolo gestore perché eseguono la stessa operazione). Nell'esempio seguente vengono illustrate le implementazioni dell'interfaccia e del gestore eventi, `TaskCompletionSource` nonché l'oggetto utilizzato per operare in modo asincrono:

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

### <a name="delete-data"></a>Elimina dati

L'eliminazione dei dati richiede un'implementazione simile. Definire un `TaskCompletionSource`oggetto, implementare un gestore eventi e il `ISoapService.DeleteTodoItemAsync` metodo:

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
