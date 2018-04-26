---
title: Visual Basic.NET Xamarin iOS e Android
description: Procedura dettagliata XThis viene illustrato come compilare App xamarin. IOS e xamarin native che utilizzano la logica di business scritta in Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 54231d42383d491678b6152e67c01c5e39a1f958
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>Visual Basic.NET Xamarin iOS e Android

Il [TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/) applicazione di esempio viene illustrato come utilizzare il codice di Visual Basic compilato in una libreria di classi portabile con Xamarin. Ecco alcune schermate delle App risultante in esecuzione su iOS, Android e Windows Phone:

 [![](native-apps-images/image5.png "iOS, Android e Windows Phone che esegue un'app compilata con Visual Basic")](native-apps-images/image5.png#lightbox)

IOS, Android e Windows Phone progetti nell'esempio vengono scritti in c#. L'interfaccia utente per ogni applicazione viene compilata con le tecnologie native (storyboard, Xml e Xaml rispettivamente), mentre il `TodoItem` management viene fornito dalla libreria di classi portabile Visual Basic usando un `IXmlStorage` implementazione fornita da il progetto nativo.

## <a name="sample-walkthrough"></a>Procedura dettagliata di esempio

Questa guida viene illustrato come Visual Basic è stato implementato nel [TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB) Xamarin di esempio per iOS e Android.

> [!NOTE]
> Esaminare le istruzioni nella [Visual Basic.NET PCLs](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/) prima di continuare con questa Guida.

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

Librerie di classi portabili Visual Basic possono essere create solo in Visual Studio.
La libreria di esempio contiene le nozioni di base dell'applicazione in quattro file di Visual Basic:

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

Poiché i comportamenti di accesso file variano enormemente tra le piattaforme, librerie di classi portabili non forniscono `System.IO` le API di archiviazione in un profilo di file. Ciò significa che se si vuole interagire direttamente con il file System nel codice portabile, è necessario richiamare i progetti nativi in ciascuna piattaforma.  Mediante la scrittura di codice Visual Basic su un'interfaccia semplice che può essere implementata in c# in ciascuna piattaforma, possiamo abbiamo condivisibile Visual Basic (codice) che può ancora accedere al file system.

Il codice di esempio utilizza questa interfaccia molto semplice che contiene solo due metodi: leggere e scrivere un file Xml serializzato.

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS, Android e Windows Phone implementazioni per questa interfaccia verranno visualizzate in un secondo momento nella Guida.

### <a name="todoitemvb"></a>TodoItem.vb

Questa classe contiene l'oggetto business da utilizzare in tutta l'applicazione. Verranno definito in Visual Basic e i progetti che vengono scritti in c# condivisi con iOS, Android e Windows Phone.

La definizione della classe è illustrata di seguito:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

L'esempio Usa la serializzazione XML e la deserializzazione per caricare e salvare gli oggetti TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

La classe di gestione presenta l'API per codice portabile. Fornisce operazioni CRUD di base per la `TodoItem` classe, ma nessuna implementazione di queste operazioni.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
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

Il costruttore accetta un'istanza di IXmlStorage come parametro. In questo modo ogni piattaforma per fornire la propria implementazione di lavoro viene garantita codice portabile descrivono altre funzionalità che possono essere condivisi.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

La classe di repository contiene la logica per la gestione dell'elenco di oggetti TodoItem. Il codice completo è illustrato di seguito: la logica esiste principalmente per gestire un valore di ID univoco tra i TodoItems come vengono aggiunti e rimossi dalla raccolta.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
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
> Questo codice è riportato un esempio di un meccanismo di archiviazione di dati molto semplice.
> Viene fornito per illustrare come una libreria di classi portabile possibile codificarli un'interfaccia per accedere a funzionalità specifiche della piattaforma (in questo caso, il caricamento e salvataggio di un file Xml). Non è progettato per essere un'alternativa di database di produzione.

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS, Android e progetti di applicazioni Windows Phone

In questa sezione contiene le implementazioni specifiche della piattaforma per l'interfaccia IXmlStorage e viene illustrato l'utilizzo di ciascuna applicazione. Tutti i progetti di applicazione vengono scritti in c#.

### <a name="ios-and-android-ixmlstorage"></a>iOS e Android IXmlStorage

Xamarin. IOS e xamarin fornire completo `System.IO` funzionalità che consente di caricare e salvare il file Xml utilizzando la seguente classe facilmente:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

Nell'applicazione iOS di `TodoItemManager` e `XmlStorageImplementation` vengono creati nel **appdelegate. cs** file come illustrato nel frammento di codice seguente. Le prime quattro righe compila solo il percorso del file in cui verranno archiviati dati; le ultime due righe vengono illustrate le due classi viene creata un'istanza.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

Nell'applicazione Android di `TodoItemManager` e `XmlStorageImplementation` vengono creati nel **Application.cs** file come illustrato nel frammento di codice seguente. Le prime tre righe compila solo il percorso del file in cui verranno archiviati dati; le ultime due righe vengono illustrate le due classi viene creata un'istanza.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

Il resto del codice dell'applicazione è principalmente in questione con l'interfaccia utente e l'uso di `TaskMgr` classe per caricare e salvare `TodoItem` classi.

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone non fornisce accesso completo al file system del dispositivo invece di esporre l'API IsolatedStorage. Il `IXmlStorage` implementazione per Windows Phone è simile al seguente:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

Il `TodoItemManager` e `XmlStorageImplementation` vengono creati nel **App.xaml.cs** file come illustrato nel frammento di codice seguente.

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

Il resto dell'applicazione Windows Phone è costituito da Xaml e c# per creare l'interfaccia utente e usare il `TodoMgr` classe per caricare e salvare `TodoItem` oggetti.

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic, libreria di classi Portabile in Visual Studio per Mac

Visual Studio per Mac non supporta il linguaggio Visual Basic: non è possibile creare o compilare progetti di Visual Basic con Visual Studio per Mac.

Visual Studio per il supporto del Mac per librerie di classi portabili significa che gli assembly libreria di classi Portabile che è sono compilati da Visual Basic può fare riferimento.

In questa sezione viene illustrato come compilare un assembly libreria di classi Portabile in Visual Studio e quindi verificare che verranno archiviato in un sistema di controllo della versione e a cui fa riferimento da altri progetti.

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Mantenere l'output di libreria di classi Portabile da Visual Studio

Per impostazione predefinita la maggior parte dei sistemi di controllo di versione (incluso TFS e Git) verranno configurati per ignorare la **/bin/** directory che significa che l'assembly compilato PCL non verranno archiviati. Ciò significa che è necessario copiare manualmente ai computer che esegue Visual Studio per Mac aggiungere un riferimento a esso.

Per garantire che il sistema di controllo delle versioni è possibile archiviare l'output di assembly libreria di classi Portabile, è possibile creare uno script di post-compilazione che copia nella radice del progetto. Questo passaggio di post-compilazione consente di verificare l'assembly può essere facilmente aggiunti al controllo del codice sorgente e condivisi con altri progetti.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Pulsante destro del mouse sul progetto e scegliere il **proprietà > eventi di compilazione** sezione.

2. Aggiungere un _post-compilazione_ script che copia la DLL di output di questo progetto nella directory radice del progetto (che è di fuori di **/bin/**). A seconda della configurazione di controllo di versione, la DLL dovrebbe ora essere possibile da aggiungere al controllo del codice sorgente.

  [![](native-apps-images/image6-vs-sml.png "Eventi di compilazione dello script di compilazione post per copiare la DLL VB")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  Pulsante destro del mouse sul progetto e scegliere **proprietà > compilare** , quindi verificare tutte le configurazioni sia selezionata nella casella di strisce alto a sinistra. Fare clic su di **eventi di compilazione...**  pulsante nella parte inferiore destra.

    [![](native-apps-images/image6.png "Sezione di compilare le proprietà del progetto")](native-apps-images/image6.png#lightbox)

1.  Aggiungere uno script di post-compilazione che copia la DLL di output di questo progetto nella directory radice del progetto (che è di fuori di **/bin/** ). A seconda della configurazione di controllo di versione, la DLL dovrebbe ora essere possibile da aggiungere al controllo del codice sorgente.

    [![](native-apps-images/image7.png "Finestra degli eventi di compilazione")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>Tutte le versioni

Successiva compilazione del progetto, verrà copiato l'assembly della libreria di classi portabile per la radice del progetto e check-in o commit o il push delle modifiche la DLL si archiviati (in modo che si possono essere scaricato in un Mac con Visual Studio per Mac).

  [![](native-apps-images/image8-sml.png "Percorso del file di output Visual Basic assembly")](native-apps-images/image8.png#lightbox)


Questo assembly possa aggiungere ai progetti Xamarin in Visual Studio per Mac, anche se il linguaggio Visual Basic non è supportato nei progetti Android o Xamarin iOS.

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>Riferimento a libreria di classi Portabile in Visual Studio per Mac

Xamarin supporta Visual Basic in grado di caricare il progetto libreria di classi Portabile (né l'app di Windows Phone) come illustrato in questo screenshot:

 [![](native-apps-images/image9.png "Visual Studio per la soluzione Mac")](native-apps-images/image9.png#lightbox)

È ancora possibile includere la DLL dell'assembly di Visual Basic PCL nei progetti di xamarin. IOS e xamarin:

1.  Fare clic su di **riferimenti** nodo e selezionare **Modifica riferimenti...**

    [![](native-apps-images/image10.png "Menu di riferimenti di progetto modifica")](native-apps-images/image10.png#lightbox)

1.  Selezionare il **.Net Assembly** scheda e passare all'output DLL nella directory del progetto di Visual Basic. Anche se Visual Studio per Mac non è possibile aprire il progetto, tutti i file devono essere presente dal controllo del codice sorgente. Fare clic su **Aggiungi** quindi **OK** per aggiungere questo assembly per le applicazioni iOS e Android.

    [![](native-apps-images/image11-sml.png "Fare clic su Aggiungi, quindi OK per aggiungere questo assembly per le applicazioni iOS e Android")](native-apps-images/image11.png#lightbox)

1.  Le applicazioni iOS e Android possono ora includere la logica dell'applicazione fornita dalla libreria di classi portabile Visual Basic. Questa schermata mostra un'applicazione iOS che fa riferimento la libreria di classi Portabile Visual Basic e include il codice che utilizza la funzionalità di libreria.

    [![](native-apps-images/image12-sml.png "Modifica riferimenti aggiungere finestra assembly .NET")](native-apps-images/image12.png#lightbox)


Se vengono apportate modifiche al progetto Visual Basic in Visual Studio ricordarsi di compilare il progetto, archiviare la DLL dell'assembly risultante in controllo del codice sorgente e quindi spostare la nuova DLL dal controllo del codice sorgente su Mac in modo che Visual Studio per Mac compila contengono l'ultima versione funzionalità.


## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come utilizzare il codice di Visual Basic nelle applicazioni Xamarin con Visual Studio e librerie di classi portabili. Anche se Xamarin non supporta direttamente Visual Basic, la compilazione di Visual Basic in una PCL consente a codice scritto con Visual Basic da includere nell'App iOS e Android.

## <a name="related-links"></a>Collegamenti correlati

- [TaskyPortableVB (esempio)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [Lo sviluppo multipiattaforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
