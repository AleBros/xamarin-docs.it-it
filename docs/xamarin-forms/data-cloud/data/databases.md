---
title: Database locali Xamarin.Forms
description: Xamarin.Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. Questo articolo descrive come le applicazioni Xamarin.Forms possono leggere e scrivere dati in un database SQLite locale usando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
ms.openlocfilehash: 2369afc693940d83971a43877da363e2c66b31b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992405"
---
# <a name="xamarinforms-local-databases"></a>Database locali Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

Il motore di database SQLite consente alle applicazioni Xamarin.Forms di caricare e salvare oggetti dati nel codice condiviso. L'applicazione di esempio usa una tabella di database SQLite per archiviare gli elementi todo. In questo articolo viene descritto come utilizzare SQLite.Net nel codice condiviso per archiviare e recuperare informazioni in un database locale.

[![Screenshot dell'app Todolist su iOS e Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "App Todolist su iOS e Android")

Integrazione di SQLite.NET nelle app per dispositivi mobili attenendosi alla procedura seguente:

1. [Installare il pacchetto NuGet](#install-the-sqlite-nuget-package).
1. [Configurare le costanti](#configure-app-constants).
1. [Creare una classe di accesso al database](#create-a-database-access-class).
1. [Accedere ai dati in Xamarin.Forms](#access-data-in-xamarinforms).
1. [Configurazione avanzata](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Installare il pacchetto SQLite NuGet

Utilizzare Gestione pacchetti NuGet per cercare **sqlite-net-pcl** e aggiungere la versione più recente al progetto di codice condiviso.

Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:

- **Creato da:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Collegamento a NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Nonostante il nome del pacchetto, usare il pacchetto NuGet **sqlite-net-pcl** anche nei progetti .NET Standard.

## <a name="configure-app-constants"></a>Configurare le costanti dell'app

Il progetto di esempio include un file **di Constants.cs** che fornisce dati di configurazione comuni:The sample project includes a data that provides common configuration data:

```csharp
public static class Constants
{
    public const string DatabaseFilename = "TodoSQLite.db3";

    public const SQLite.SQLiteOpenFlags Flags =
        // open the database in read/write mode
        SQLite.SQLiteOpenFlags.ReadWrite |
        // create the database if it doesn't exist
        SQLite.SQLiteOpenFlags.Create |
        // enable multi-threaded database access
        SQLite.SQLiteOpenFlags.SharedCache;

    public static string DatabasePath
    {
        get
        {
            var basePath = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
            return Path.Combine(basePath, DatabaseFilename);
        }
    }
}
```

Il file delle costanti `SQLiteOpenFlag` specifica i valori di enumerazione predefiniti utilizzati per inizializzare la connessione al database. L'enum supporta questi valori:The `SQLiteOpenFlag` enum supports these values:

- `Create`: la connessione creerà automaticamente il file di database se non esiste.
- `FullMutex`: la connessione viene aperta in modalità threading serializzato.
- `NoMutex`: la connessione viene aperta in modalità multithreading.
- `PrivateCache`: la connessione non parteciperà alla cache condivisa, anche se è abilitata.
- `ReadWrite`: la connessione può leggere e scrivere dati.
- `SharedCache`: la connessione parteciperà alla cache condivisa, se abilitata.
- `ProtectionComplete`: il file è crittografato e inaccessibile mentre il dispositivo è bloccato.
- `ProtectionCompleteUnlessOpen`: il file viene crittografato fino all'apertura, ma è quindi accessibile anche se l'utente blocca il dispositivo.
- `ProtectionCompleteUntilFirstUserAuthentication`: il file viene crittografato fino a quando l'utente non ha avviato e sbloccato il dispositivo.
- `ProtectionNone`: il file di database non è crittografato.

Potrebbe essere necessario specificare flag diversi a seconda di come verrà utilizzato il database. Per ulteriori `SQLiteOpenFlags`informazioni su , vedere [Apertura di una nuova connessione al database](https://www.sqlite.org/c3ref/open.html) in sqlite.org.

## <a name="create-a-database-access-class"></a>Creare una classe di accesso al databaseCreate a database access class

Una classe wrapper di database astrae il livello di accesso ai dati dal resto dell'app. Questa classe centralizza la logica di query e semplifica la gestione dell'inizializzazione del database, semplificando il refactoring o l'espansione delle operazioni sui dati man mano che l'app cresce. L'app Todo `TodoItemDatabase` definisce una classe a questo scopo.

### <a name="lazy-initialization"></a>Inizializzazione differita

Utilizza `TodoItemDatabase` la classe `Lazy` .NET per ritardare l'inizializzazione del database fino al primo accesso. L'uso dell'inizializzazione differita impedisce al processo di caricamento del database di ritardare l'avvio dell'app. Per ulteriori informazioni, vedere [Classe&lt;T&gt; pigro](xref:System.Lazy`1).

```csharp
public class TodoItemDatabase
{
    static readonly Lazy<SQLiteAsyncConnection> lazyInitializer = new Lazy<SQLiteAsyncConnection>(() =>
    {
        return new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    });

    static SQLiteAsyncConnection Database => lazyInitializer.Value;
    static bool initialized = false;

    public TodoItemDatabase()
    {
        InitializeAsync().SafeFireAndForget(false);
    }

    async Task InitializeAsync()
    {
        if (!initialized)
        {
            if (!Database.TableMappings.Any(m => m.MappedType.Name == typeof(TodoItem).Name))
            {
                await Database.CreateTablesAsync(CreateFlags.None, typeof(TodoItem)).ConfigureAwait(false);
                initialized = true;
            }
        }
    }

    //...
}
```

La connessione al database è un campo statico che assicura che venga utilizzata una singola connessione al database per tutta la durata dell'app. L'uso di una connessione statica persistente offre prestazioni migliori rispetto all'apertura e alla chiusura delle connessioni più volte durante una singola sessione dell'app.

Il `InitializeAsync` metodo è responsabile del controllo `TodoItem` se esiste già una tabella per l'archiviazione degli oggetti. Questo metodo crea automaticamente la tabella se non esiste.

### <a name="the-safefireandforget-extension-method"></a>Il metodo di estensione SafeFireAndForgetThe The SafeFireAndForget extension method

Quando `TodoItemDatabase` viene creata un'istanza della classe, è necessario inizializzare la connessione al database, ovvero un processo asincrono. Tuttavia:

- I costruttori di classe non possono essere asincroni.
- Un metodo asincrono che non è atteso non genererà eccezioni.
- L'utilizzo del `Wait` metodo blocca il thread _e_ ingoia le eccezioni.

Per avviare l'inizializzazione asincrona, evitare di bloccare l'esecuzione e avere `SafeFireAndForget`la possibilità di intercettare le eccezioni, l'applicazione di esempio utilizza un metodo di estensione denominato . Il `SafeFireAndForget` metodo di estensione `Task` fornisce funzionalità aggiuntive alla classe:The extension method provides additional functionality to the class:

```csharp
public static class TaskExtensions
{
    // NOTE: Async void is intentional here. This provides a way
    // to call an async method from the constructor while
    // communicating intent to fire and forget, and allow
    // handling of exceptions
    public static async void SafeFireAndForget(this Task task,
        bool returnToCallingContext,
        Action<Exception> onException = null)
    {
        try
        {
            await task.ConfigureAwait(returnToCallingContext);
        }

        // if the provided action is not null, catch and
        // pass the thrown exception
        catch (Exception ex) when (onException != null)
        {
            onException(ex);
        }
    }
}
```

Il `SafeFireAndForget` metodo attende l'esecuzione `Task` asincrona dell'oggetto fornito `Action` e consente di collegare un oggetto che viene chiamato se viene generata un'eccezione.

Per ulteriori informazioni, consultate [Modello asincrono basato su attività (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Metodi di manipolazione dei dati

La `TodoItemDatabase` classe include metodi per i quattro tipi di modifica dei dati: creazione, lettura, modifica ed eliminazione. La libreria SQLite.NET fornisce una semplice mappa orM (Object Relational Map) che consente di archiviare e recuperare oggetti senza scrivere istruzioni SQL.

```csharp
public class TodoItemDatabase {

    // ...

    public Task<List<TodoItem>> GetItemsAsync()
    {
        return Database.Table<TodoItem>().ToListAsync();
    }

    public Task<List<TodoItem>> GetItemsNotDoneAsync()
    {
        // SQL queries are also possible
        return Database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
    }

    public Task<TodoItem> GetItemAsync(int id)
    {
        return Database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
    }

    public Task<int> SaveItemAsync(TodoItem item)
    {
        if (item.ID != 0)
        {
            return Database.UpdateAsync(item);
        }
        else
        {
            return Database.InsertAsync(item);
        }
    }

    public Task<int> DeleteItemAsync(TodoItem item)
    {
        return Database.DeleteAsync(item);
    }
}
```

## <a name="access-data-in-xamarinforms"></a>Accedere ai dati in Xamarin.Forms

La classe Xamarin.Forms `App` espone un'istanza della `TodoItemDatabase` classe:

```csharp
static TodoItemDatabase database;
public static TodoItemDatabase Database
{
    get
    {
        if (database == null)
        {
            database = new TodoItemDatabase();
        }
        return database;
    }
}
```

Questa proprietà consente ai componenti Xamarin.Forms di `Database` chiamare il recupero dei dati e i metodi di modifica sull'istanza in risposta all'interazione dell'utente. Ad esempio:

```csharp
var saveButton = new Button { Text = "Save" };
saveButton.Clicked += async (sender, e) =>
{
    var todoItem = (TodoItem)BindingContext;
    await App.Database.SaveItemAsync(todoItem);
    await Navigation.PopAsync();
};
```

## <a name="advanced-configuration"></a>Configurazione avanzata

SQLite fornisce un'API affidabile con più funzionalità di quelle trattate in questo articolo e nell'app di esempio. Nelle sezioni seguenti vengono illustrate le funzionalità importanti per la scalabilità.

Per altre informazioni, vedere [Documentazione di SQLite](https://www.sqlite.org/docs.html) sui sqlite.org.

### <a name="write-ahead-logging"></a>Registrazione Write-Ahead

Per impostazione predefinita, SQLite utilizza un journal di rollback tradizionale. Una copia del contenuto del database non modificato viene scritta in un file di rollback separato, quindi le modifiche vengono scritte direttamente nel file di database. COMMIT si verifica quando viene eliminato il journal di rollback.

Write-Ahead Logging (WAL) scrive prima le modifiche in un file WAL separato. In modalità WAL, un COMMIT è un record speciale, aggiunto al file WAL, che consente più transazioni in un singolo file WAL. Un file WAL viene unito nuovamente al file di database in un'operazione speciale denominata _checkpoint_.

WAL può essere più veloce per i database locali perché lettori e writer non si bloccano a vicenda, consentendo la concomizione delle operazioni di lettura e scrittura. Tuttavia, la modalità WAL non consente modifiche alle dimensioni della _pagina,_ aggiunge ulteriori associazioni di file al database e aggiunge l'operazione di _checkpoint_ aggiuntiva.

Per abilitare WAL in `EnableWriteAheadLoggingAsync` SQLite.NET, `SQLiteAsyncConnection` chiamare il metodo sull'istanza:To enable WAL in SQLite.NET, call the method on the instance:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Per altre informazioni, vedere [SQLite Write-Ahead Logging](https://www.sqlite.org/wal.html) su sqlite.org.

### <a name="copying-a-database"></a>Copia di un database

Esistono diversi casi in cui potrebbe essere necessario copiare un database SQLite:There are several cases where it may be necessary to copy a SQLite database:

- Un database è stato fornito con l'applicazione, ma deve essere copiato o spostato in un archivio scrivibile sul dispositivo mobile.
- È necessario eseguire un backup o una copia del database.
- È necessario eseguire la versione, spostare o rinominare il file di database.

In generale, lo spostamento, la ridenominazione o la copia di un file di database è lo stesso processo di qualsiasi altro tipo di file con alcune considerazioni aggiuntive:In general, moving, renaming, or copying a database file is the same process as any other file type with a few additional considerations:

- Tutte le connessioni al database devono essere chiuse prima di tentare di spostare il file di database.
- Se si utilizza [la registrazione Write-Ahead](#write-ahead-logging), SQLite creerà un file di accesso alla memoria condivisa (shm) e un file (Write Ahead Log) (con estensione wal). Assicurarsi di applicare le modifiche anche a questi file.

Per ulteriori informazioni, vedere [Gestione dei file in Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Collegamenti correlati

- [Applicazione di esempio TodoTodo sample application](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [SQLite.NET pacchetto NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentazione di SQLite](https://www.sqlite.org/docs.html)
- [Uso di SQLite con AndroidUsing SQLite with Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Uso di SQLite con iOSUsing SQLite with iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Modello asincrono basato su attività (TAP)Task-based asynchronous pattern (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Classe&lt;T&gt; pigro](xref:System.Lazy`1)
