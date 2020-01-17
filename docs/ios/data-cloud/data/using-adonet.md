---
title: Uso di ADO.NET con Novell. iOS
description: Questo documento descrive come usare ADO.NET come metodo per accedere a SQLite in un'applicazione Novell. iOS. Vengono illustrati i riferimenti agli assembly, mono. Data. sqlite e l'esempio BasicDataAccess.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2ed16c651d0b373e33d58bb73591977d3484d6e0
ms.sourcegitcommit: be8ce3449afab22673e48b546d857431c071d66f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76162935"
---
# <a name="using-adonet-with-xamarinios"></a>Uso di ADO.NET con Novell. iOS

Novell include il supporto predefinito per il database SQLite disponibile in iOS, esposto mediante una nota sintassi simile a ADO.NET. L'uso di queste API richiede la scrittura di istruzioni SQL elaborate da SQLite, ad esempio `CREATE TABLE`, `INSERT` e istruzioni `SELECT`.

## <a name="assembly-references"></a>Riferimenti dell'assembly

Per usare l'accesso a SQLite tramite ADO.NET, è necessario aggiungere `System.Data` e `Mono.Data.Sqlite` riferimenti al progetto iOS, come illustrato di seguito (per gli esempi in Visual Studio per Mac e Visual Studio):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Assembly References in Visual Studio for Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Assembly References in Visual Studio")

-----

Fare clic con il pulsante destro del mouse su **riferimenti > modifica riferimenti...** quindi fare clic per selezionare gli assembly necessari.

## <a name="about-monodatasqlite"></a>Informazioni su mono. Data. sqlite

Si utilizzerà la classe `Mono.Data.Sqlite.SqliteConnection` per creare un file di database vuoto e quindi creare un'istanza di `SqliteCommand` oggetti che è possibile utilizzare per eseguire istruzioni SQL sul database.

1. **Creazione di un database vuoto** : chiamare il metodo `CreateFile` con un percorso di file valido (IE. Writeable). È necessario verificare se il file esiste già prima di chiamare questo metodo. in caso contrario, verrà creato un nuovo database (vuoto) nella parte superiore del vecchio e i dati nel file precedente andranno perduti:

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > La variabile `dbPath` deve essere determinata in base alle regole descritte in precedenza in questo documento.

2. **Creazione di una connessione al database** : dopo che è stato creato il file di database SQLite, è possibile creare un oggetto connessione per accedere ai dati. La connessione viene costruita con una stringa di connessione che assume la forma di `Data Source=file_path`, come illustrato di seguito:

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Come indicato in precedenza, una connessione non deve mai essere riutilizzata tra thread diversi. In caso di dubbi, creare la connessione come richiesto e chiuderla al termine dell'operazione. Tuttavia, tenere presente questo problema più spesso del necessario.

3. **Creazione ed esecuzione di un comando di database** : una volta che è presente una connessione, è possibile eseguire comandi SQL arbitrari. Nel codice riportato di seguito viene illustrata un'istruzione CREATE TABLE eseguita.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

Quando si esegue SQL direttamente sul database, è consigliabile evitare di eseguire richieste non valide, ad esempio il tentativo di creare una tabella già esistente. Tenere traccia della struttura del database in modo che non si verifichi una Sqliteexception, ad esempio "tabella degli errori SQLite [Items] già esistente".

## <a name="basic-data-access"></a>Accesso ai dati di base

Il codice di esempio *DataAccess_Basic* per questo documento è simile al seguente quando viene eseguito in iOS:

 ![](using-adonet-images/image9.png "iOS ADO.NET sample")

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

Poiché SQLite consente l'esecuzione di comandi SQL arbitrari sui dati, è possibile eseguire qualsiasi istruzione di creazione, inserimento, aggiornamento, eliminazione o selezione. Per informazioni sui comandi SQL supportati da SQLite, vedere il sito Web SQLite. Le istruzioni SQL vengono eseguite utilizzando uno dei tre metodi su un oggetto SqliteCommand:

- **ExecuteNonQuery** : usato in genere per la creazione di tabelle o l'inserimento di dati. Il valore restituito per alcune operazioni è il numero di righe interessate; in caso contrario, è-1.
- **ExecuteReader** : usato quando una raccolta di righe deve essere restituita come `SqlDataReader`.
- **ExecuteScalar** : Recupera un singolo valore (ad esempio un'aggregazione).

### <a name="executenonquery"></a>EXECUTENONQUERY

Le istruzioni INSERT, UPDATE e DELETE restituiranno il numero di righe interessate. Tutte le altre istruzioni SQL restituiranno-1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

Il metodo seguente mostra una clausola WHERE nell'istruzione SELECT. Poiché il codice sta creando un'istruzione SQL completa, è necessario eseguire l'escape dei caratteri riservati, ad esempio le virgolette (') intorno alle stringhe.

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

Il metodo ExecuteReader restituisce un oggetto SqliteDataReader. Oltre al metodo Read illustrato nell'esempio, altre proprietà utili includono:

- **RowsAffected** : numero di righe interessate dalla query.
- **HasRows** : indica se sono state restituite righe.

### <a name="executescalar"></a>EXECUTESCALAR

Usare questa istruzione per istruzioni SELECT che restituiscono un singolo valore, ad esempio un'aggregazione.

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Il tipo restituito del metodo `ExecuteScalar` è `object`: è necessario eseguire il cast del risultato in base alla query del database. Il risultato può essere un numero intero da una query COUNT o una stringa da una singola colonna SELECT query. Si noti che questa operazione è diversa dagli altri metodi Execute che restituiscono un oggetto Reader o un conteggio del numero di righe interessate.

## <a name="microsoftdatasqlite"></a>Microsoft.Data.Sqlite

È presente un'altra libreria `Microsoft.Data.Sqlite`, che può essere [installata da NuGet](https://www.nuget.org/packages/Microsoft.Data.Sqlite), equivalente dal punto di vista funzionale a `Mono.Data.Sqlite` e consente gli stessi tipi di query.

Si verifica un [confronto tra le due librerie](https://docs.microsoft.com/dotnet/standard/data/sqlite/compare) e alcuni [dettagli specifici di Novell](https://docs.microsoft.com/dotnet/standard/data/sqlite/xamarin). Più importante per le app Novell. iOS, è necessario includere una chiamata di inizializzazione:

```csharp
// required for Xamarin.iOS
SQLitePCL.Batteries_V2.Init();
```

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
