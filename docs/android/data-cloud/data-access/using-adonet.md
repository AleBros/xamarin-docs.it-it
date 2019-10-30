---
title: Uso di ADO.NET con Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: 6592bd6d5cf7b78918fa2d020be723d662625e06
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023765"
---
# <a name="using-adonet-with-android"></a>Uso di ADO.NET con Android

Novell include il supporto predefinito per il database SQLite disponibile in Android e può essere esposto usando una nota sintassi simile a ADO.NET. L'uso di queste API richiede la scrittura di istruzioni SQL elaborate da SQLite, ad esempio `CREATE TABLE`, `INSERT` e istruzioni `SELECT`.

## <a name="assembly-references"></a>Riferimenti dell'assembly

Per usare l'accesso a SQLite tramite ADO.NET, è necessario aggiungere `System.Data` e `Mono.Data.Sqlite` riferimenti al progetto Android, come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows) 

![Riferimenti Android in Visual Studio](using-adonet-images/image7.png "Riferimenti Android in Visual Studio") 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos) 

![Riferimenti Android in Visual Studio per Mac](using-adonet-images/image5.png "Riferimenti Android in Visual Studio per Mac") 

-----

Fare clic con il pulsante destro del mouse su **riferimenti > modifica riferimenti...** quindi fare clic per selezionare gli assembly necessari.

## <a name="about-monodatasqlite"></a>Informazioni su mono. Data. sqlite

Si utilizzerà la classe `Mono.Data.Sqlite.SqliteConnection` per creare un file di database vuoto e quindi creare un'istanza di `SqliteCommand` oggetti che è possibile utilizzare per eseguire istruzioni SQL sul database.

**Creazione di un database vuoto** &ndash; chiamare il metodo `CreateFile` con un percorso di file valido (ovvero scrivibile). È necessario verificare se il file esiste già prima di chiamare questo metodo. in caso contrario, verrà creato un nuovo database (vuoto) nella parte superiore del vecchio e i dati nel file precedente andranno persi.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` è necessario determinare la variabile `dbPath` in base alle regole descritte in precedenza in questo documento.

La **creazione di una connessione di database** &ndash; dopo che è stato creato il file di database SQLite è possibile creare un oggetto connessione per accedere ai dati. La connessione viene costruita con una stringa di connessione che assume la forma di `Data Source=file_path`, come illustrato di seguito:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Come indicato in precedenza, una connessione non deve mai essere riutilizzata tra thread diversi. In caso di dubbi, creare la connessione come richiesto e chiuderla al termine dell'operazione. Tuttavia, tenere presente questo problema più spesso del necessario.

**La creazione e l'esecuzione di un comando di Database** &ndash; una volta che è presente una connessione a cui è possibile eseguire comandi SQL arbitrari. Nel codice riportato di seguito viene illustrata un'istruzione `CREATE TABLE` eseguita.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

Quando si esegue SQL direttamente sul database, è consigliabile evitare di eseguire richieste non valide, ad esempio il tentativo di creare una tabella già esistente. Tenere traccia della struttura del database in modo da non causare un `SqliteException` come la tabella degli **errori SQLite [Items] esiste già**.

## <a name="basic-data-access"></a>Accesso ai dati di base

Il codice di esempio *DataAccess_Basic* per questo documento è simile al seguente quando viene eseguito in Android:

![Esempio di Android ADO.NET](using-adonet-images/image8.png "Esempio di Android ADO.NET")

Il codice seguente illustra come eseguire semplici operazioni SQLite e visualizzare i risultati in come testo nella finestra principale dell'applicazione.

È necessario includere gli spazi dei nomi seguenti:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

Nell'esempio di codice riportato di seguito viene illustrata un'intera interazione con il database:

1. Creazione del file di database
2. Inserimento di alcuni dati
3. Esecuzione di una query sui dati

Queste operazioni vengono in genere visualizzate in più posizioni nel codice. ad esempio, è possibile creare il file di database e le tabelle quando l'applicazione viene avviata per la prima volta ed eseguire operazioni di lettura e scrittura dei dati nelle singole schermate dell'app. Nell'esempio seguente sono stati raggruppati in un unico metodo per questo esempio:

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}

```

## <a name="more-complex-queries"></a>Query più complesse

Poiché SQLite consente l'esecuzione di comandi SQL arbitrari sui dati, è possibile eseguire qualsiasi operazione `CREATE`, `INSERT`, `UPDATE`, `DELETE`o `SELECT` istruzioni desiderate. Per informazioni sui comandi SQL supportati da SQLite, vedere il sito Web SQLite. Le istruzioni SQL vengono eseguite utilizzando uno dei tre metodi su un oggetto `SqliteCommand`:

- **ExecuteNonQuery** &ndash; in genere utilizzato per la creazione della tabella o l'inserimento di dati. Il valore restituito per alcune operazioni è il numero di righe interessate; in caso contrario, è-1.

- **ExecuteReader** &ndash; usato quando una raccolta di righe deve essere restituita come `SqlDataReader`.

- **ExecuteScalar** &ndash; recupera un singolo valore, ad esempio un'aggregazione.

### <a name="executenonquery"></a>EXECUTENONQUERY

le istruzioni `INSERT`, `UPDATE`e `DELETE` restituiranno il numero di righe interessate. Tutte le altre istruzioni SQL restituiranno-1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

Il metodo seguente mostra una clausola `WHERE` nell'istruzione `SELECT`.
Poiché il codice sta creando un'istruzione SQL completa, è necessario eseguire l'escape dei caratteri riservati, ad esempio le virgolette (') intorno alle stringhe.

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

Il metodo `ExecuteReader` restituisce un oggetto `SqliteDataReader`. Oltre al metodo `Read` illustrato nell'esempio, altre proprietà utili includono:

- **RowsAffected** &ndash; conteggio delle righe interessate dalla query.

- **HasRows** &ndash; se sono state restituite righe.

### <a name="executescalar"></a>EXECUTESCALAR

Utilizzare questo oggetto per `SELECT` istruzioni che restituiscono un singolo valore, ad esempio un'aggregazione.

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Il tipo restituito del metodo `ExecuteScalar` è `object` &ndash; è necessario eseguire il cast del risultato a seconda della query del database. Il risultato può essere un numero intero da una query `COUNT` o una stringa da una singola colonna `SELECT` query. Si noti che questa operazione è diversa dagli altri metodi di `Execute` che restituiscono un oggetto Reader o un conteggio del numero di righe interessate.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
