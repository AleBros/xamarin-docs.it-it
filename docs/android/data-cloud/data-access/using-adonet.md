---
title: Utilizzo di ADO.NET
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: a2f7a7a0c282284d7a45fb81c134d300aef5afba
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="using-adonet"></a>Utilizzo di ADO.NET

Xamarin offre supporto predefinito per il database SQLite è disponibile in Android e possa essere esposte tramite sintassi ADO.NET familiarità. Utilizzo di queste API richiede la scrittura di istruzioni SQL che vengono elaborate SQLite, ad esempio `CREATE TABLE`, `INSERT` e `SELECT` istruzioni.

## <a name="assembly-references"></a>Riferimenti dell'assembly

Per usare l'accesso SQLite tramite ADO.NET, è necessario aggiungere `System.Data` e `Mono.Data.Sqlite` fa riferimento al progetto Android, come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin) 

![I riferimenti di Android in Visual Studio](using-adonet-images/image7.png "Android fa riferimento in Visual Studio") 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac) 

![I riferimenti di Android in Visual Studio per Mac](using-adonet-images/image5.png "Android fa riferimento in Visual Studio per Mac") 

-----


Fare doppio clic su **riferimenti > modificare i riferimenti...**  , quindi fare clic per selezionare gli assembly necessari.

## <a name="about-monodatasqlite"></a>About Mono.Data.Sqlite

Si utilizzerà il `Mono.Data.Sqlite.SqliteConnection` classe per creare un file di database vuoto e quindi creare un'istanza `SqliteCommand` oggetti che è possibile utilizzare per eseguire istruzioni SQL nel database.

**Creazione di un Database vuoto** &ndash; chiamare il `CreateFile` metodo con un valore valido (ie. scrivibile) percorso del file. Controllare se il file esiste già prima di chiamare questo metodo, in caso contrario verrà creato un nuovo database (vuoto) di sopra di quello vecchio e i dati nel vecchio file andranno persi.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` Il `dbPath` variabile deve essere determinata in base a regole illustrate in precedenza in questo documento.

**Creazione di una connessione di Database** &ndash; dopo aver creato il file di database SQLite è possibile creare un oggetto di connessione per accedere ai dati. La connessione è stata creata con una stringa di connessione sotto forma di `Data Source=file_path`, come illustrato di seguito:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Come accennato in precedenza, una connessione non deve essere mai utilizzata nuovamente tra thread diversi. In caso di dubbio, creare la connessione come richiesto e chiuderlo dopo averli; Tuttavia, tenere in considerazione di eseguire il più spesso rispetto a quanto richiesto troppo.

**Per creare ed eseguire un comando di Database** &ndash; una volta che una connessione, è possibile eseguire comandi SQL arbitrari su di essa. Il codice seguente viene illustrato un `CREATE TABLE` istruzione eseguita.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

Quando si esegue SQL direttamente nel database è consigliabile adottare le precauzioni normale non effettuare richieste non valide, ad esempio il tentativo di creare una tabella già esistente. Tenere traccia della struttura del database in modo che non si provochi un `SqliteException` , ad esempio **esiste già una tabella degli errori di SQLite [elementi]**.

## <a name="basic-data-access"></a>Accesso ai dati di base

Il *DataAccess_Basic* codice di esempio per questo documento è simile al seguente quando si esegue in Android:

![Esempio di ADO.NET Android](using-adonet-images/image8.png "esempio ADO.NET Android")

Il codice riportato di seguito viene illustrato come eseguire semplici operazioni di SQLite e vengono illustrati i risultati in formato testo nella finestra principale dell'applicazione.

È necessario includere questi spazi dei nomi:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

Esempio di codice seguente viene illustrata l'interazione di un intero database:

1.  Creazione del file di database
2.  Inserimento di alcuni dati.
3.  Esecuzione di una query sui dati

Queste operazioni in genere appariranno in più posizioni all'interno del codice, ad esempio è possibile creare il file di database e tabelle al primo avvio dell'applicazione ed eseguire dati letture e scritture in singole schermate dell'app. Nell'esempio riportato di seguito sono state raggruppate in un unico metodo per questo esempio:

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

Poiché SQLite consente arbitrari comandi SQL da eseguire sui dati, è possibile eseguire qualsiasi `CREATE`, `INSERT`, `UPDATE`, `DELETE`, o `SELECT` istruzioni desiderato. È possibile leggere sui comandi SQL supportati da SQLite nel sito Web di Sqlite. Le istruzioni SQL vengono eseguite utilizzando uno dei tre metodi su un `SqliteCommand` oggetto:

-   **ExecuteNonQuery** &ndash; in genere utilizzato per l'inserimento di dati o di creazione tabella. Il valore restituito per alcune operazioni è il numero di righe interessate, in caso contrario è -1.

-   **ExecuteReader** &ndash; utilizzato quando una raccolta di righe deve essere restituita come un `SqlDataReader`.

-   **ExecuteScalar** &ndash; recupera un valore singolo (ad esempio una funzione di aggregazione).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`, `UPDATE`, e `DELETE` istruzioni verranno restituito il numero di righe interessate. Tutte le altre istruzioni SQL restituirà -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

Il metodo seguente viene illustrato un `WHERE` clausola il `SELECT` istruzione.
Poiché il codice consiste nel creare un'istruzione SQL completa è necessario prestare particolare attenzione per eseguire l'escape di caratteri riservati, ad esempio le virgolette (') racchiudere le stringhe.

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

Il metodo `ExecuteReader` restituisce un oggetto `SqliteDataReader`. Oltre al `Read` metodo illustrato nell'esempio, includere altre proprietà utili:

-   **RowsAffected** &ndash; conteggio delle righe interessate dalla query.

-   **HasRows** &ndash; indica se le righe restituite.


### <a name="executescalar"></a>EXECUTESCALAR

Utilizzare questo modulo `SELECT` istruzioni che restituiscono un singolo valore (ad esempio, una funzione di aggregazione).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Il `ExecuteScalar` è di tipo restituito del metodo `object` &ndash; è necessario il cast del risultato in base alla query del database. Il risultato potrebbe essere un numero intero compreso tra un `COUNT` query o una stringa da una singola colonna `SELECT` query. Si noti che questo è diverso da altri `Execute` metodi che restituiscono un oggetto lettore o un conteggio del numero di righe interessate.



## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recipe dati Android](https://developer.xamarin.com/recipes/android/data/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
