---
title: Visual Basic in Xamarin.Android e Xamarin.iOS
description: Questa procedura dettagliata illustra come compilare app Xamarin.iOS e Xamarin.Android native che usano la logica di business scritta in Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 9f227f51596a4ed93fd830c3f3495a90c1f7f722
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014557"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Visual Basic in Xamarin Android e iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

L'applicazione di esempio [TaskyVB](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/) illustra come Visual Basic codice compilato in una libreria di .NET standard può essere usato con Xamarin. Ecco alcune schermate delle app risultanti eseguite in Android e iOS:

 [![Android e iOS che esegue un'app compilata con Visual Basic](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

I progetti Android e iOS nell'esempio sono tutti scritti in C#. L'interfaccia utente per ogni applicazione è compilata con tecnologie native, mentre la gestione del `TodoItem` viene fornita dalla libreria di .NET Standard Visual Basic utilizzando un file XML (a scopo dimostrativo, non un database completo).

## <a name="sample-walkthrough"></a>Scenario di esempio

Questa guida illustra come Visual Basic è stata implementata nell'esempio [TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) Xamarin per iOS e Android.

> [!NOTE]
> Prima di continuare con questa guida, vedere le istruzioni su [Visual Basic e .NET standard](index.md) .
>
> Per informazioni su come creare un'app con codice Visual Basic dell'interfaccia utente condivisa, vedere [Xamarin.Forms usando](xamarin-forms.md) le istruzioni Visual Basic.

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

È possibile creare Visual Basic librerie .NET Standard solo in Visual Studio in Windows.
La libreria di esempio contiene le nozioni di base dell'applicazione in questi file di Visual Basic:

- TodoItem. vb
- TodoItemManager. vb
- TodoItemRepositoryXML. vb
- Xmlstorage. vb

### <a name="todoitemvb"></a>TodoItem. vb

Questa classe contiene l'oggetto business da utilizzare nell'applicazione. Verrà definito in Visual Basic e condiviso con i progetti Android e iOS scritti in C#.

La definizione della classe è illustrata di seguito:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

Nell'esempio viene utilizzata la serializzazione e la deserializzazione XML per caricare e salvare gli oggetti TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager. vb

La classe Manager presenta l'"API" per il codice portatile. Fornisce operazioni CRUD di base per la classe `TodoItem`, ma nessuna implementazione di tali operazioni.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

Il costruttore accetta un'istanza di IXmlStorage come parametro. In questo modo, ogni piattaforma può fornire la propria implementazione di lavoro pur continuando a consentire al codice portabile di descrivere altre funzionalità che possono essere condivise.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository. vb

La classe del repository contiene la logica per la gestione dell'elenco di oggetti TodoItem. Il codice completo è illustrato di seguito: la logica esiste principalmente per gestire un valore ID univoco in TodoItems Man mano che vengono aggiunti e rimossi dalla raccolta.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String)
        _filename = filename
        _storage = New XmlStorage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> Questo codice è un esempio di un meccanismo di archiviazione dati di base.
> Viene fornito per dimostrare in che modo una libreria di .NET Standard può codificare in base a un'interfaccia per accedere alle funzionalità specifiche della piattaforma, in questo caso il caricamento e il salvataggio di un file XML. E non deve essere un'alternativa di database di qualità di produzione.

## <a name="android-and-ios-application-projects"></a>Progetti di applicazioni Android e iOS

### <a name="ios"></a>iOS

Nell'applicazione iOS i `TodoItemManager` e i `XmlStorageImplementation` vengono creati nel file **AppDelegate.cs** , come illustrato in questo frammento di codice. Le prime quattro righe stanno semplicemente creando il percorso del file in cui verranno archiviati i dati. le due righe finali mostrano le due classi di cui viene creata un'istanza.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

Nell'applicazione Android il `TodoItemManager` e il `XmlStorageImplementation` vengono creati nel file **Application.cs** , come illustrato in questo frammento di codice. Le prime tre righe stanno semplicemente creando il percorso del file in cui verranno archiviati i dati. le due righe finali mostrano le due classi di cui viene creata un'istanza.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

Il resto del codice dell'applicazione riguarda principalmente l'interfaccia utente e l'uso della classe `TaskMgr` per caricare e salvare `TodoItem` classi.

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 per Mac

> [!WARNING]
> Visual Studio per Mac non supporta la modifica della lingua del Visual Basic. non sono disponibili voci di menu per la creazione di progetti o file Visual Basic. Se si apre a **. vb** , non è presente alcuna evidenziazione della sintassi del linguaggio, completamento automatico o IntelliSense.

Visual Studio 2019 per Mac _può_ compilare i progetti di visual studio .NET standard creati in Windows, quindi le app iOS possono fare riferimento a tali progetti.

Visual Studio 2017 _non è in grado_ di compilare progetti Visual Basic.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare Visual Basic codice nelle applicazioni Xamarin con Visual Studio e le librerie di .NET Standard. Anche se Xamarin non supporta direttamente Visual Basic, la compilazione di Visual Basic in una libreria .NET Standard consente l'inclusione di codice scritto con Visual Basic nelle app iOS e Android.

## <a name="related-links"></a>Collegamenti correlati

- [TaskyVB (esempio .NET Standard)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [Novità di .NET Standard](https://docs.microsoft.com/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)
