---
title: Uso di SQLite.NET con Android
description: La libreria NuGet SQLite.NET PCL fornisce un semplice meccanismo di accesso ai dati per le app Xamarin.Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: ff59213a730b74b916b993c48a6f5f779149a6f9
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488075"
---
# <a name="using-sqlitenet-with-android"></a>Uso di SQLite.NET con Android

La libreria SQLite.NET consigliata da Xamarin è un ORM di base che consente di archiviare e recuperare facilmente gli oggetti nel database SQLite locale in un dispositivo Android. ORM sta per il mapping relazionale a oggetti &ndash; un'API che consente di salvare e recuperare "oggetti" da un database senza scrivere istruzioni SQL.

Per includere la libreria SQLite.NET in un'app Xamarin, aggiungere il pacchetto NuGet seguente al progetto:

- **Nome pacchetto:** sqlite-net-PCL
- **Autore:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **URL:** [NuGet.org/packages/sqlite-net-PCL](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacchetto NuGet SQLite.NET](using-sqlite-orm-images/image1a-sml.png "Pacchetto NuGet SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Sono disponibili numerosi pacchetti SQLite diversi. Assicurarsi di scegliere quello corretto (potrebbe non essere il risultato principale nella ricerca).

Una volta disponibile la libreria SQLite.NET, attenersi ai tre passaggi seguenti per utilizzarla per accedere a un database:

1. **Aggiungere un'istruzione using** &ndash; aggiungere la seguente istruzione ai C# file in cui è necessario l'accesso ai dati:

    ```csharp
    using SQLite;
    ```

2. **Creazione di un database vuoto** &ndash; è possibile creare un riferimento al database passando il percorso del file al costruttore della classe SQLiteConnection. Non è necessario verificare se il file esiste già &ndash; verrà creato automaticamente se necessario. in caso contrario, il file di database esistente verrà aperto. La variabile `dbPath` deve essere determinata in base alle regole descritte in precedenza in questo documento:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3. **Salvare i dati** &ndash; dopo aver creato un oggetto SQLiteConnection, i comandi di database vengono eseguiti chiamando i relativi metodi, ad esempio CreateTable e INSERT come segue:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4. **Recuperare i dati** &ndash; per recuperare un oggetto o un elenco di oggetti, usare la sintassi seguente:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Esempio di accesso ai dati di base

Il *DataAccess_Basic* codice di esempio per questo documento è simile al seguente quando viene eseguito in Android. Il codice illustra come eseguire semplici operazioni SQLite.NET e Mostra i risultati in come testo nella finestra principale dell'applicazione.

**Android**

![Esempio di Android SQLite.NET](using-sqlite-orm-images/image3.png "Esempio di Android SQLite.NET")

Nell'esempio di codice riportato di seguito viene illustrata un'intera interazione del database utilizzando la libreria SQLite.NET per incapsulare l'accesso al database sottostante.
Mostra:

1. Creazione del file di database

2. Inserimento di alcuni dati mediante la creazione di oggetti e il relativo salvataggio

3. Esecuzione di una query sui dati

È necessario includere gli spazi dei nomi seguenti:

```csharp
using SQLite; // from the github SQLite.cs class
```

L'ultimo richiede l'aggiunta di SQLite al progetto. Si noti che la tabella di database SQLite viene definita aggiungendo attributi a una classe (la classe `Stock`) invece di un comando CREATE TABLE.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

Se si utilizza l'attributo `[Table]` senza specificare un parametro del nome di tabella, la tabella di database sottostante avrà lo stesso nome della classe, in questo caso "stock". Il nome effettivo della tabella è importante se si scrivono query SQL direttamente sul database anziché utilizzare i metodi di accesso ai dati ORM. Analogamente, l'attributo `[Column("_id")]` è facoltativo e, in assenza di una colonna, verrà aggiunta alla tabella con lo stesso nome della proprietà nella classe.

## <a name="sqlite-attributes"></a>Attributi SQLite

Gli attributi comuni che è possibile applicare alle classi per controllare il modo in cui vengono archiviati nel database sottostante includono:

- **[PrimaryKey]** &ndash; questo attributo può essere applicato a una proprietà integer per forzarlo come chiave primaria della tabella sottostante. Le chiavi primarie composte non sono supportate.

- **[Incremento automatico]** &ndash; questo attributo provocherà l'incremento automatico del valore di una proprietà integer per ogni nuovo oggetto inserito nel database

- **[Column (Name)]** &ndash; il parametro `name` imposta il nome della colonna del database sottostante.

- **[Table (Name)]** &ndash; contrassegna la classe come in grado di essere archiviata in una tabella SQLite sottostante con il nome specificato.

- **[MaxLength (value)]** &ndash; limitare la lunghezza di una proprietà di testo quando viene eseguito un tentativo di inserimento del database. L'utilizzo del codice deve essere convalidato prima di inserire l'oggetto perché questo attributo è solo ' checked ' quando si tenta di eseguire un'operazione di inserimento o aggiornamento di un database.

- **[Ignore]** &ndash; fa in modo che SQLite.NET ignori questa proprietà.
    Questa operazione è particolarmente utile per le proprietà che dispongono di un tipo che non può essere archiviato nel database o di proprietà che non possono essere risolte automaticamente da SQLite.

- **[Unique]** &ndash; garantisce che i valori nella colonna del database sottostante siano univoci.

La maggior parte di questi attributi è facoltativa. È sempre necessario specificare una chiave primaria Integer in modo che le query di selezione ed eliminazione possano essere eseguite in modo efficiente sui dati.

## <a name="more-complex-queries"></a>Query più complesse

Per eseguire altre operazioni sui dati, è possibile usare i metodi seguenti `SQLiteConnection`:

- **Insert** &ndash; aggiunge un nuovo oggetto al database.

- **Get&lt;t&gt;** &ndash; tenta di recuperare un oggetto utilizzando la chiave primaria.

- **Table&lt;t&gt;** &ndash; restituisce tutti gli oggetti nella tabella.

- **Elimina** &ndash; Elimina un oggetto utilizzando la relativa chiave primaria.

- **Query&lt;t&gt;** &ndash; eseguire una query SQL che restituisce un numero di righe, come oggetti.

- **Eseguire** &ndash; utilizzare questo metodo (e non `Query`) quando non si prevede che le righe vengano restituite da SQL, ad esempio istruzioni INSERT, Update e DELETE.

### <a name="getting-an-object-by-the-primary-key"></a>Recupero di un oggetto in base alla chiave primaria

SQLite.Net fornisce il metodo Get per recuperare un singolo oggetto in base alla relativa chiave primaria.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selezione di un oggetto mediante LINQ

I metodi che restituiscono raccolte supportano `IEnumerable<T>` pertanto è possibile utilizzare LINQ per eseguire query o ordinare il contenuto di una tabella. Nel codice seguente viene illustrato un esempio di utilizzo di LINQ per filtrare tutte le voci che iniziano con la lettera "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selezione di un oggetto tramite SQL

Anche se SQLite.Net è in grado di fornire l'accesso basato su oggetti ai dati, a volte potrebbe essere necessario eseguire una query più complessa di quella consentita da LINQ (oppure potrebbe essere necessaria una maggiore velocità di prestazioni). È possibile usare i comandi SQL con il metodo di query, come illustrato di seguito:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Quando si scrivono direttamente istruzioni SQL, si crea una dipendenza dai nomi di tabelle e colonne del database, che sono stati generati dalle classi e dai relativi attributi. Se si modificano i nomi nel codice, è necessario ricordare di aggiornare le istruzioni SQL scritte manualmente.

### <a name="deleting-an-object"></a>Eliminazione di un oggetto

La chiave primaria viene utilizzata per eliminare la riga, come illustrato di seguito:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

È possibile controllare l'`rowcount` per verificare il numero di righe interessate (eliminate in questo caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Uso di SQLite.NET con più thread

SQLite supporta tre diverse modalità di threading, ovvero a *thread singolo*, a thread *multipli*e *serializzati*. Se si desidera accedere al database da più thread senza alcuna restrizione, è possibile configurare SQLite per l'utilizzo della modalità di threading **serializzata** . È importante impostare questa modalità all'inizio dell'applicazione (ad esempio, all'inizio del metodo di `OnCreate`).

Per modificare la modalità di threading, chiamare `SqliteConnection.SetConfig`. Questa riga di codice, ad esempio, configura SQLite per la modalità **serializzata** :

```csharp
using using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

La versione Android di SQLite presenta una limitazione che richiede alcuni passaggi. Se la chiamata a `SqliteConnection.SetConfig` produce un'eccezione SQLite, ad esempio `library used incorrectly`, è necessario usare la soluzione alternativa seguente:

1. Collegarsi alla libreria **libsqlite.so** nativa in modo che le API `sqlite3_shutdown` e `sqlite3_initialize` siano rese disponibili per l'app:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```

2. All'inizio del metodo `OnCreate` aggiungere questo codice a Shutdown SQLite, configurarlo per la modalità **serializzata** e reinizializzare sqlite:

    ```csharp
    using using Mono.Data.Sqlite;
    ...
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Questa soluzione alternativa funziona anche per la libreria `Mono.Data.Sqlite`. Per ulteriori informazioni su SQLite e multithread, vedere [SQLite e più thread](https://www.sqlite.org/threadsafe.html).

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Accesso ai dati di Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
