---
title: Uso di ADO.NET con Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 76a66b4dbde65a3fc44d3490e0147ff66b088466
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525975"
---
# <a name="using-adonet-with-android"></a>Uso di ADO.NET con Android

Novell include il supporto predefinito per il database SQLite disponibile in Android e può essere esposto usando una nota sintassi simile a ADO.NET. L'uso di queste API richiede la scrittura di istruzioni SQL elaborate da SQLite, ad `CREATE TABLE`esempio `INSERT` le `SELECT` istruzioni e.

## <a name="assembly-references"></a>Riferimenti dell'assembly

Per usare l'accesso a SQLite tramite ADO.NET, `System.Data` è `Mono.Data.Sqlite` necessario aggiungere i riferimenti al progetto Android, come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows) 

![Riferimenti Android in Visual Studio](using-adonet-images/image7.png "Riferimenti Android in Visual Studio") 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos) 

![Riferimenti Android in Visual Studio per Mac](using-adonet-images/image5.png "Riferimenti Android in Visual Studio per Mac") 

-----


Fare clic con il pulsante destro del mouse su **riferimenti > modifica riferimenti...** quindi fare clic per selezionare gli assembly necessari.

## <a name="about-monodatasqlite"></a>Informazioni su mono. Data. sqlite

Verrà utilizzata la `Mono.Data.Sqlite.SqliteConnection` classe per creare un file di database vuoto e quindi per creare `SqliteCommand` un'istanza degli oggetti che è possibile utilizzare per eseguire istruzioni SQL sul database.

**Creazione di un database vuoto** &ndash; Chiamare il`CreateFile` metodo con un percorso di file valido, ad esempio scrivibile. È necessario verificare se il file esiste già prima di chiamare questo metodo. in caso contrario, verrà creato un nuovo database (vuoto) nella parte superiore del vecchio e i dati nel file precedente andranno persi.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`La `dbPath` variabile deve essere determinata in base alle regole descritte in precedenza in questo documento.

**Creazione di una connessione al database** &ndash; Dopo aver creato il file di database SQLite, è possibile creare un oggetto connessione per accedere ai dati. La connessione viene costruita con una stringa di connessione che assume la forma `Data Source=file_path`di, come illustrato di seguito:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Come indicato in precedenza, una connessione non deve mai essere riutilizzata tra thread diversi. In caso di dubbi, creare la connessione come richiesto e chiuderla al termine dell'operazione. Tuttavia, tenere presente questo problema più spesso del necessario.

**Creazione ed esecuzione di un comando di database** &ndash; Quando si dispone di una connessione, è possibile eseguire comandi SQL arbitrari su di essa. Nel codice riportato di seguito `CREATE TABLE` viene illustrata l'esecuzione di un'istruzione.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

Quando si esegue SQL direttamente sul database, è consigliabile evitare di eseguire richieste non valide, ad esempio il tentativo di creare una tabella già esistente. Tenere traccia della struttura del database in modo che non si verifichi un `SqliteException` errore, ad esempio la tabella degli **errori SQLite [Items] esiste già**.

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

Poiché SQLite consente l'esecuzione di comandi SQL arbitrari sui dati, è possibile `CREATE`eseguire qualsiasi `INSERT` `UPDATE` `DELETE`istruzione,,, o `SELECT` che si desidera. Per informazioni sui comandi SQL supportati da SQLite, vedere il sito Web SQLite. Le istruzioni SQL vengono eseguite utilizzando uno dei tre metodi su un `SqliteCommand` oggetto:

- **ExecuteNonQuery** &ndash; Usato in genere per la creazione di tabelle o l'inserimento di dati. Il valore restituito per alcune operazioni è il numero di righe interessate; in caso contrario, è-1.

- **ExecuteReader** Utilizzato quando una raccolta di righe deve essere restituita `SqlDataReader`come. &ndash;

- **ExecuteScalar** &ndash; Recupera un singolo valore (ad esempio un'aggregazione).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`le `UPDATE`istruzioni, `DELETE` e restituiranno il numero di righe interessate. Tutte le altre istruzioni SQL restituiranno-1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

Il metodo seguente mostra una `WHERE` clausola `SELECT` nell'istruzione.
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

Il metodo `ExecuteReader` restituisce un oggetto `SqliteDataReader`. Oltre al `Read` metodo illustrato nell'esempio, altre proprietà utili includono:

- **RowsAffected** &ndash; Conteggio delle righe interessate dalla query.

- **HasRows** &ndash; Indica se sono state restituite righe.


### <a name="executescalar"></a>EXECUTESCALAR

Usare questa istruzione `SELECT` per le istruzioni che restituiscono un singolo valore, ad esempio un'aggregazione.

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Il `ExecuteScalar` tipo restituito del metodo è `object` &ndash; è necessario eseguire il cast del risultato a seconda della query del database. Il risultato può essere un numero intero di `COUNT` una query o una stringa da una query `SELECT` a colonna singola. Si noti che questa operazione è diversa `Execute` dagli altri metodi che restituiscono un oggetto Reader o un conteggio del numero di righe interessate.



## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
