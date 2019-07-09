---
title: Uso di SQLite.NET con Android
description: La libreria NuGet PCL SQLite.NET fornisce un meccanismo di accesso semplice dei dati per le app xamarin. Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/18/2018
ms.openlocfilehash: 27d2d68243c5b25a3ecfc0ce333dca183ba090ec
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649530"
---
# <a name="using-sqlitenet-with-android"></a>Uso di SQLite.NET con Android

La libreria di SQLite.NET in cui si consiglia di Xamarin è un prodotto ORM molto semplice che consente di archiviare e recuperare gli oggetti nel database SQLite locale in un dispositivo Android facilmente. È l'acronimo di Mapping relazionale oggetti ORM &ndash; un'API che consente di salvare e recuperare "oggetti" da un database senza scrittura di istruzioni SQL.

Per includere la libreria di SQLite.NET in un'app Xamarin, aggiungere il pacchetto NuGet seguente al progetto:

- **Nome del pacchetto:** sqlite-net-libreria di classi portabile
- **Autore:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacchetto NuGet di SQLite.NET](using-sqlite-orm-images/image1a-sml.png "SQLite.NET mobileengagement")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Sono disponibili numerosi pacchetti SQLite diversi, assicurarsi di scegliere quello corretto (potrebbe non essere il risultato superiore nella ricerca).

Dopo aver creato la libreria di SQLite.NET disponibile, seguire questi tre passaggi per usarlo per accedere a un database:

1.  **Aggiungere using informativa** &ndash; aggiungere la seguente istruzione per il C# file in cui è richiesto l'accesso ai dati:

    ```csharp
    using SQLite;
    ```

2.  **Creare un Database vuoto** &ndash; un riferimento al database può essere creata da passando il percorso del file al costruttore di classe SQLiteConnection. Non è necessario verificare se il file esiste già &ndash; verrà automaticamente creata se necessario, in caso contrario, verrà aperto il file di database esistente. Il `dbPath` variabile deve essere determinata in base alla regole illustrate in precedenza in questo documento:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3.  **Salvare i dati** &ndash; dopo aver creato un oggetto SQLiteConnection, vengono eseguiti i comandi di database chiamando i metodi, ad esempio CreateTable e inserimento simile al seguente:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4.  **Recupero dei dati** &ndash; per recuperare un oggetto (o un elenco di oggetti) usa la sintassi seguente:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Dati di base di accesso di esempio

Il *DataAccess_Basic* codice di esempio per questo documento aspetto simile al seguente durante l'esecuzione in Android. Il codice viene illustrato come eseguire semplici operazioni di SQLite.NET e vengono illustrati i risultati in formato testo nella finestra principale dell'applicazione.


**Android**

![Esempio di Android SQLite.NET](using-sqlite-orm-images/image3.png "esempio SQLite.NET Android")

Esempio di codice seguente mostra un'interazione intero database usando la libreria di SQLite.NET per incapsulare l'accesso al database sottostante.
Mostra:

1.  Creazione del file di database

2.  Inserire alcuni dati tramite la creazione di oggetti e quindi salvando i

3.  Esecuzione di una query sui dati

È necessario includere questi spazi dei nomi:

```csharp
using SQLite; // from the github SQLite.cs class
```

Il penultimo richiede che sia stato aggiunto SQLite al progetto. Si noti che aggiungendo attributi a una classe viene definita la tabella di database SQLite (il `Stock` classe) invece di un comando CREATE TABLE.

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

Uso di `[Table]` attributo senza specificare un parametro di nome di tabella causerà la tabella di database sottostante avere lo stesso nome di classe (in questo caso, "Stock"). Il nome della tabella effettiva è importante se si scrivono query SQL direttamente nel database anziché utilizzare i metodi di accesso ORM dati. Allo stesso modo di `[Column("_id")]` attributo è facoltativo e se assente una colonna verrà aggiunta alla tabella con lo stesso nome della proprietà nella classe.

## <a name="sqlite-attributes"></a>Attributi di SQLite

Attributi comuni che è possibile applicare alle classi per controllare come vengono archiviati nel database sottostante includono:

-   **[PrimaryKey]**  &ndash; Questo attributo può essere applicato a una proprietà integer per forzarlo a diventare la chiave primaria della tabella sottostante. Le chiavi primarie composte non sono supportate.

-   **[Incremento automatico]**  &ndash; Questo attributo causerà il valore della proprietà integer a essere a incremento automatico per ogni nuovo oggetto inserito nel database

-   **[Column(name)]**  &ndash; Specificando l'opzione facoltativa `name` parametro sostituirà il valore predefinito del nome della colonna di database sottostante (che è lo stesso come proprietà).

-   **[Table(name)]**  &ndash; Contrassegna la classe come la possibilità di essere archiviati in una tabella di SQLite sottostante. Specificando il parametro name facoltativo, il valore predefinito del nome della tabella di database sottostante, ovvero lo stesso nome della classe, verrà ignorato.

-   **[MaxLength(value)]**  &ndash; Limitare la lunghezza di una proprietà di testo, quando viene tentata l'inserimento di un database. Utilizzo di codice deve convalidare questo prima dell'inserimento dell'oggetto quando questo attributo è solo 'checked' quando l'inserimento di un database o l'operazione di aggiornamento viene tentata.

-   **[Ignorare]**  &ndash; SQLite.NET fa sì che per ignorare questa proprietà.
    Ciò è particolarmente utile per le proprietà che hanno un tipo che non possa essere archiviato nel database, o proprietà che definiscono raccolte non possono essere risolti automaticamente SQLite.

-   **[Unique]**  &ndash; Assicura che i valori nella colonna del database sottostante siano univoci.


La maggior parte di questi attributi sono facoltativa, SQLite userà i valori predefiniti per i nomi di tabella e colonna. È sempre necessario specificare una chiave primaria di tipo integer in modo che le query di selezione e l'eliminazione possono essere eseguite in modo efficiente ai dati.

## <a name="more-complex-queries"></a>Query più complesse

I metodi seguenti per `SQLiteConnection` può essere utilizzato per eseguire altre operazioni sui dati:

-   **Inserire** &ndash; aggiunge un nuovo oggetto nel database.

-   **Ottenere&lt;T&gt;**  &ndash; tenta di recuperare un oggetto usando la chiave primaria.

-   **Nella tabella&lt;T&gt;**  &ndash; restituisce tutti gli oggetti della tabella.

-   **Eliminare** &ndash; Elimina un oggetto usando la chiave primaria.

-   **Query&lt;T&gt;**  &ndash; eseguire una query SQL che restituisce un numero di righe (come oggetti).

-   **Eseguire** &ndash; utilizzare questo metodo (e non `Query`) quando non si prevede che le righe da SQL (ad esempio istruzioni INSERT, UPDATE e DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Ottenere un oggetto per la chiave primaria

SQLite.Net fornisce il metodo Get per recuperare un singolo oggetto in base alla chiave primaria.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selezione di un oggetto usando Linq

I metodi che restituiscono raccolte supportano `IEnumerable<T>` quindi è possibile usare Linq per eseguire query o ordinare il contenuto di una tabella. Il codice seguente illustra un esempio di utilizzo di Linq per filtrare tutte le voci che iniziano con la lettera "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selezione di un oggetto usando SQL

Anche se SQLite.Net può fornire l'accesso basato su oggetti per i dati, in alcuni casi potrebbe essere necessario eseguire una query più complessa rispetto a Linq consente (o potrebbe essere necessario migliorare le prestazioni). È possibile usare i comandi SQL con il metodo di Query, come illustrato di seguito:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Durante la scrittura di istruzioni SQL direttamente crei una dipendenza sui nomi delle tabelle e colonne nel database, che sono state generate dalle classi e i relativi attributi. Se si modificano i nomi nel codice è necessario ricordarsi di aggiornare tutte le istruzioni SQL scritte manualmente.

### <a name="deleting-an-object"></a>Eliminazione di un oggetto

La chiave primaria viene usata per eliminare la riga, come illustrato di seguito:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

È possibile controllare il `rowcount` per confermare il numero di righe interessato (in questo caso eliminati).

## <a name="using-sqlitenet-with-multiple-threads"></a>Uso di SQLite.NET con più thread

SQLite supporta tre diverse modalità di threading: *Thread singolo*, *multithread*, e *serializzato*. Se si desidera accedere al database da più thread senza restrizioni, è possibile configurare SQLite per usare la **serializzata** threading modalità. È importante impostare questa modalità nelle prime fasi dell'applicazione (ad esempio, all'inizio del `OnCreate` (metodo)).

Per modificare la modalità di threading, chiamare `SqliteConnection.SetConfig`. Ad esempio, questa riga di codice Configura per SQLite **serializzata** modalità:

```csharp
using using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

La versione Android di SQLite prevede una limitazione che richiede pochi altri passaggi. Se la chiamata a `SqliteConnection.SetConfig` genera un'eccezione di SQLite, ad esempio `library used incorrectly`, sarà necessario usare la soluzione alternativa seguente:

1.  Collegamento all'oggetto nativo **libsqlite.so** libreria in modo che le `sqlite3_shutdown` e `sqlite3_initialize` API vengono resi disponibili per l'app:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```

2.  All'inizio del `OnCreate` metodo, aggiungere questo codice al arresto SQLite, configurarlo per **serializzata** modalità e reinizializzare SQLite:

    ```csharp
    using using Mono.Data.Sqlite;
    ...
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Questa soluzione funziona anche per il `Mono.Data.Sqlite` libreria. Per altre informazioni su SQLite e il multithreading, vedere [SQLite e in più thread](https://www.sqlite.org/threadsafe.html).

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
