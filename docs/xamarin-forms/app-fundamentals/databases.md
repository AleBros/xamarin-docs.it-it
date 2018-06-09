---
title: Database locale di xamarin. Forms
description: Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. In questo articolo viene descritto come xamarin. Forms applicazioni possono leggere e scrivere dati in un database locale di SQLite utilizzando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 91df4d36dd8d98712063a30773f927a82676b18e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243610"
---
# <a name="xamarinforms-local-databases"></a>Database locale di xamarin. Forms

_Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. In questo articolo viene descritto come xamarin. Forms applicazioni possono leggere e scrivere dati in un database locale di SQLite utilizzando SQLite.Net._

## <a name="overview"></a>Panoramica

Xamarin. Forms applicazioni possono utilizzare il [NuGet PCL SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) pacchetto per incorporare le operazioni di database in codice condiviso facendo riferimento al `SQLite` classi disponibili in NuGet. È possibile definire le operazioni del database nel progetto di libreria Standard di .NET della soluzione xamarin. Forms, con i progetti specifici della piattaforma restituzione di un percorso a dove verrà archiviato il database.

Il tipo di accompagnamento [applicazione di esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) è una semplice applicazione elenco attività. Le schermate seguenti mostrano come nell'esempio viene visualizzata in ogni piattaforma:

[![Schermate di esempio xamarin. Forms database](databases-images/todo-list-sml.png "TodoList prima Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList prima Page Screenshots") [ ![ Schermate di esempio xamarin. Forms database](databases-images/todo-list-sml.png "TodoList prima Page Screenshots")](databases-images/todo-list.png#lightbox "Page Screenshots prima TodoList")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Utilizzo di SQLite

In questa sezione viene illustrato come aggiungere pacchetti SQLite.Net NuGet a una soluzione xamarin. Forms, scrivere metodi per eseguire operazioni di database, utilizzare il [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) per determinare una posizione per archiviare il database in ogni piattaforma.

<a name="XamarinForms_PCL_Project" />

### <a name="xamarinsforms-net-standard-or-pcl-project"></a>Xamarins.Forms .NET Standard o un progetto libreria di classi Portabile

Per aggiungere il supporto di SQLite a un progetto xamarin. Forms, utilizzare la funzione di ricerca di NuGet per trovare **sqlite-net-libreria di classi portabile** e installare il pacchetto:

![Aggiungere NuGet SQLite.NET PCL pacchetto](databases-images/vs2017-sqlite-pcl-nuget.png "aggiungere NuGet SQLite.NET PCL pacchetto")

Esistono un numero di pacchetti NuGet con nomi simili, il pacchetto corretto questi attributi è:

- **Creato da:** Frank A. Krueger
- **ID:** sqlite-net-libreria di classi portabile
- **Collegamento di NuGet:** [sqlite-net-libreria di classi portabile](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!TIP]
> Usare la **sqlite-net-libreria di classi portabile** NuGet anche in progetti .NET Standard.

Dopo aver aggiunto il riferimento, scrivere un'interfaccia per l'astrazione di funzionalità specifiche della piattaforma, per determinare la posizione del file di database. L'interfaccia utilizzata in questo esempio definisce un singolo metodo:

```csharp
public interface IFileHelper
{
  string GetLocalFilePath(string filename);
}
```

Una volta definito l'interfaccia, utilizzare il [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) per ottenere un'implementazione, un percorso locale (si noti che questa interfaccia non è ancora stata implementata). Il codice seguente ottiene un'implementazione `App.Database` proprietà:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(DependencyService.Get<IFileHelper>().GetLocalFilePath("TodoSQLite.db3"));
    }
    return database;
  }
}
```

Il `TodoItemDatabase` costruttore è illustrato di seguito:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Questo approccio crea una connessione di singolo database che viene lasciata aperta durante l'esecuzione dell'applicazione, evitando così il costo di apertura e chiusura del file di database ogni volta che viene eseguita un'operazione di database.

Il resto della `TodoItemDatabase` classe contiene le query SQLite eseguite multipiattaforma. Di seguito è riportato il codice di query di esempio (ulteriori dettagli sulla sintassi, vedere il [SQLite.NET utilizzando](~/cross-platform/app-fundamentals/index.md) articolo):

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> Il vantaggio di utilizzare l'API SQLite.Net asincrona è il database in cui operazioni vengono spostate al thread in background. Inoltre, non è necessario scrivere ulteriore concorrenza codice di gestione in quanto l'API si occupa di esso.

Tutti i viene scritto il codice di accesso ai dati nel progetto libreria di classi Portabile per essere condivisi tra tutte le piattaforme. Solo un percorso file locale per il database necessarie codice specifico della piattaforma, come descritto nelle sezioni seguenti.

<a name="PCL_iOS" />

### <a name="ios-project"></a>Progetto iOS

Per configurare l'applicazione iOS, aggiungere lo stesso pacchetto NuGet per il progetto iOS utilizzando il *NuGet* finestra:

![Aggiungere NuGet SQLite.NET PCL pacchetto](databases-images/vsmac-sqlite-nuget.png "aggiungere NuGet SQLite.NET PCL pacchetto")

È l'unico codice richiesto il `IFileHelper` implementazione che determina il percorso del file di dati. Il codice seguente inserisce i file di database SQLite nel **/database di libreria** cartella all'interno di sandbox dell'applicazione. Vedere il [iOS funziona con il File System](~/ios/app-fundamentals/file-system.md) documentazione per ulteriori informazioni sulle diverse directory disponibili per l'archiviazione.

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.iOS
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
      string docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
      string libFolder = Path.Combine(docFolder, "..", "Library", "Databases");

      if (!Directory.Exists(libFolder))
      {
        Directory.CreateDirectory(libFolder);
      }

      return Path.Combine(libFolder, filename);
    }
  }
}
```

Si noti che il codice include il `assembly:Dependency` attributo in modo che sia individuabile per questa implementazione il `DependencyService`.

<a name="PCL_Android" />

### <a name="android-project"></a>Progetto Android

Per configurare l'applicazione di Android, aggiungere lo stesso pacchetto NuGet per il progetto Android utilizzando il *NuGet* finestra:

![](databases-images/vsmac-sqlite-nuget.png "Aggiungere NuGet SQLite.NET PCL pacchetto")

Dopo aver aggiunto questo riferimento, l'unico codice richiesto è il `IFileHelper` implementazione che determina il percorso del file di dati.

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.Droid
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
        string path = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        return Path.Combine(path, filename);
    }
  }
}
```

<a name="PCL_UWP" />

### <a name="windows-10-universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform) di Windows 10

Per configurare l'applicazione UWP, aggiungere lo stesso pacchetto NuGet per il progetto UWP utilizzando il *NuGet* finestra:

![Aggiungere NuGet SQLite.NET PCL pacchetto](databases-images/vs2017-sqlite-uwp-nuget.png "aggiungere NuGet SQLite.NET PCL pacchetto")

Dopo aver aggiunto il riferimento, implementare il `IFileHelper` interfaccia mediante la specifica della piattaforma `Windows.Storage` API per determinare il percorso del file di dati.

```csharp
using Windows.Storage;
...

[assembly: Dependency(typeof(FileHelper))]
namespace Todo.UWP
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
      return Path.Combine(ApplicationData.Current.LocalFolder.Path, filename);
    }
  }
}
```

## <a name="summary"></a>Riepilogo

Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso.

In questo articolo è incentrata sulla **accesso** un database SQLite con xamarin. Forms. Per ulteriori informazioni sull'uso di SQLite.Net stesso, consultare il [SQLite.NET in Android](~/android/data-cloud/data-access/using-sqlite-orm.md) oppure [SQLite.NET in iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentazione. Gran parte del codice SQLite.Net è condivisibile in tutte le piattaforme; Configura solo il percorso del file di database SQLite richiede funzionalità specifiche della piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di attività](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Cartella di lavoro database](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
