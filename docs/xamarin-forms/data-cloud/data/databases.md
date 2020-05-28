---
title: Xamarin.FormsDatabase locali
description: Xamarin.Formssupporta applicazioni basate su database che usano il motore di database SQLite, che consente di caricare e salvare oggetti nel codice condiviso. Questo articolo descrive in che modo Xamarin.Forms le applicazioni possono leggere e scrivere dati in un database SQLite locale usando SQLite.NET.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 04d813baae5796da68ea27389df33738af5cde3e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131001"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.FormsDatabase locali

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

Il motore di database SQLite consente Xamarin.Forms alle applicazioni di caricare e salvare oggetti dati nel codice condiviso. Nell'applicazione di esempio viene utilizzata una tabella di database SQLite per archiviare gli elementi todo. Questo articolo descrive come usare SQLite.Net nel codice condiviso per archiviare e recuperare le informazioni in un database locale.

[![Screenshot dell'app Todo in iOS e Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "App Todo in iOS e Android")

Integrare SQLite.NET nelle app per dispositivi mobili attenendosi alla procedura seguente:

1. [Installare il pacchetto NuGet](#install-the-sqlite-nuget-package).
1. [Configurare le costanti](#configure-app-constants).
1. [Creare una classe di accesso al database](#create-a-database-access-class).
1. [Accedere ai dati Xamarin.Forms in ](#access-data-in-xamarinforms).
1. [Configurazione avanzata](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Installare il pacchetto NuGet SQLite

Usare Gestione pacchetti NuGet per cercare **sqlite-net-PCL** e aggiungere la versione più recente al progetto di codice condiviso.

Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:

- **Creato da:** Frank A. Krueger
- **ID:** sqlite-net-PCL
- **Collegamento a NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Nonostante il nome del pacchetto, usare il pacchetto NuGet **sqlite-net-pcl** anche nei progetti .NET Standard.

## <a name="configure-app-constants"></a>Configurare le costanti dell'app

Il progetto di esempio include un file **Constants.cs** che fornisce i dati di configurazione comuni:

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

Il file Constants specifica i `SQLiteOpenFlag` valori enum predefiniti utilizzati per inizializzare la connessione al database. L' `SQLiteOpenFlag` enumerazione supporta i valori seguenti:

- `Create`: La connessione creerà automaticamente il file di database, se non esiste.
- `FullMutex`: La connessione viene aperta in modalità di threading serializzato.
- `NoMutex`: La connessione viene aperta in modalità multithread.
- `PrivateCache`: La connessione non verrà inclusa nella cache condivisa, anche se è abilitata.
- `ReadWrite`: La connessione può leggere e scrivere i dati.
- `SharedCache`: La connessione parteciperà alla cache condivisa, se abilitata.
- `ProtectionComplete`: Il file è crittografato e non è accessibile quando il dispositivo è bloccato.
- `ProtectionCompleteUnlessOpen`: Il file è crittografato fino a quando non viene aperto, ma è quindi accessibile anche se l'utente blocca il dispositivo.
- `ProtectionCompleteUntilFirstUserAuthentication`: Il file viene crittografato fino a quando l'utente non ha avviato e sbloccato il dispositivo.
- `ProtectionNone`: Il file di database non è crittografato.

Potrebbe essere necessario specificare flag diversi a seconda del modo in cui verrà utilizzato il database. Per ulteriori informazioni su `SQLiteOpenFlags` , vedere [apertura di una nuova connessione al Database](https://www.sqlite.org/c3ref/open.html) in SQLite.org.

## <a name="create-a-database-access-class"></a>Creare una classe di accesso al database

Una classe wrapper di database astrae il livello di accesso ai dati dal resto dell'app. Questa classe centralizza la logica di query e semplifica la gestione dell'inizializzazione del database, rendendo più semplice effettuare il refactoring o espandere le operazioni sui dati man mano che aumenta l'app. L'app Todo definisce una `TodoItemDatabase` classe a questo scopo.

### <a name="lazy-initialization"></a>Inizializzazione differita

`TodoItemDatabase`Usa la classe .NET `Lazy` per ritardare l'inizializzazione del database fino a quando non viene eseguito per la prima volta. L'uso dell'inizializzazione differita impedisce al processo di caricamento del database di ritardare l'avvio dell'app. Per altre informazioni, vedere [ &lt; &gt; classe Lazy T](xref:System.Lazy`1).

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

La connessione al database è un campo statico che garantisce l'uso di una singola connessione di database per tutta la durata dell'app. L'uso di una connessione statica persistente offre prestazioni migliori rispetto all'apertura e alla chiusura di connessioni più volte durante una singola sessione dell'app.

Il `InitializeAsync` metodo è responsabile della verifica dell'esistenza di una tabella per l'archiviazione di `TodoItem` oggetti. Questo metodo crea automaticamente la tabella se non esiste.

### <a name="the-safefireandforget-extension-method"></a>Metodo di estensione SafeFireAndForget

Quando `TodoItemDatabase` viene creata un'istanza della classe, è necessario inizializzare la connessione al database, che è un processo asincrono. Tuttavia:

- I costruttori di classi non possono essere asincroni.
- Un metodo asincrono che non è atteso non genererà eccezioni.
- L'utilizzo del `Wait` metodo blocca il thread _e_ ingoia le eccezioni.

Per avviare l'inizializzazione asincrona, evitare di bloccare l'esecuzione e avere la possibilità di intercettare le eccezioni, l'applicazione di esempio usa un metodo di estensione denominato `SafeFireAndForget` . Il `SafeFireAndForget` metodo di estensione fornisce funzionalità aggiuntive alla `Task` classe:

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

Il `SafeFireAndForget` metodo attende l'esecuzione asincrona dell' `Task` oggetto fornito e consente di alleghiare un oggetto `Action` che viene chiamato se viene generata un'eccezione.

Per altre informazioni, vedere [modello asincrono basato su attività (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Metodi di manipolazione dei dati

La `TodoItemDatabase` classe include metodi per i quattro tipi di manipolazione dei dati: create, Read, Edit e DELETE. La libreria SQLite.NET fornisce una semplice mappa relazionale a oggetti (ORM) che consente di archiviare e recuperare oggetti senza scrivere istruzioni SQL.

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

## <a name="access-data-in-xamarinforms"></a>Accedere ai dati inXamarin.Forms

La Xamarin.Forms `App` classe espone un'istanza della `TodoItemDatabase` classe:

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

Questa proprietà consente Xamarin.Forms ai componenti di chiamare i metodi per il recupero e la manipolazione dei dati sull' `Database` istanza in risposta all'interazione dell'utente. Ad esempio:

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

SQLite fornisce un'API affidabile con più funzionalità di quelle descritte in questo articolo e nell'app di esempio. Le sezioni seguenti illustrano le funzionalità importanti per la scalabilità.

Per ulteriori informazioni, vedere la [documentazione di SQLite](https://www.sqlite.org/docs.html) in SQLite.org.

### <a name="write-ahead-logging"></a>Registrazione write-ahead

Per impostazione predefinita, SQLite usa un journal di rollback tradizionale. Una copia del contenuto del database non modificato viene scritta in un file di rollback separato, quindi le modifiche vengono scritte direttamente nel file di database. Il COMMIT si verifica quando viene eliminato il Journal di rollback.

La registrazione write-ahead (WAL) scrive prima le modifiche in un file WAL separato. In modalità WAL, un COMMIT è un record speciale, aggiunto al file WAL, che consente l'esecuzione di più transazioni in un unico file WAL. Un file WAL viene unito di nuovo nel file di database in un'operazione speciale denominata _Checkpoint_.

WAL può essere più veloce per i database locali perché i lettori e i writer non si bloccano reciprocamente, consentendo l'esecuzione simultanea delle operazioni di lettura e scrittura. La modalità WAL, tuttavia, non consente modifiche alle _dimensioni della pagina_, aggiunge ulteriori associazioni di file al database e aggiunge l'operazione di _Checkpoint_ aggiuntiva.

Per abilitare WAL in SQLite.NET, chiamare il `EnableWriteAheadLoggingAsync` metodo sull' `SQLiteAsyncConnection` istanza:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Per altre informazioni, vedere [registrazione write-ahead di SQLite](https://www.sqlite.org/wal.html) in SQLite.org.

### <a name="copying-a-database"></a>Copia di un database

Esistono diversi casi in cui potrebbe essere necessario copiare un database SQLite:

- Un database è stato fornito con l'applicazione, ma deve essere copiato o spostato nell'archiviazione scrivibile del dispositivo mobile.
- È necessario eseguire un backup o una copia del database.
- È necessario eseguire la versione, spostare o rinominare il file di database.

In generale, lo stesso processo di trasferimento, ridenominazione o copia di un file di database è lo stesso di qualsiasi altro tipo di file con alcune considerazioni aggiuntive:

- È necessario chiudere tutte le connessioni al database prima di tentare di spostare il file di database.
- Se si usa la [registrazione write-ahead](#write-ahead-logging), SQLite creerà un file di accesso alla memoria condivisa (con estensione SHM) e un file (log write-ahead) (con estensione Wal). Assicurarsi di applicare anche le modifiche apportate a questi file.

Per ulteriori informazioni, vedere [gestione dei file Xamarin.Forms in ](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Collegamenti correlati

- [Applicazione di esempio TODO](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [Pacchetto NuGet SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentazione di SQLite](https://www.sqlite.org/docs.html)
- [Uso di SQLite con Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Uso di SQLite con iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Modello asincrono basato su attività (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [&lt;Classe Lazy T &gt;](xref:System.Lazy`1)
