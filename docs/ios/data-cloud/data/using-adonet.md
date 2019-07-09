---
title: Utilizzo di ADO.NET con xamarin. IOS
description: Questo documento descrive come usare ADO.NET come un metodo per accedere a SQLite in un'applicazione xamarin. IOS. Illustra l'esempio BasicDataAccess riferimenti ad assembly e Mono.Data.Sqlite.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 293ccc66395edbe18399717caf632292ff760b0b
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650194"
---
# <a name="using-adonet-with-xamarinios"></a>Utilizzo di ADO.NET con xamarin. IOS

Xamarin offre supporto predefinito per il database SQLite che è disponibile in iOS, esposto tramite sintassi familiare simile a ADO.NET. Usando queste API richiede la scrittura di istruzioni SQL che vengono elaborate da SQLite, ad esempio `CREATE TABLE`, `INSERT` e `SELECT` istruzioni.

## <a name="assembly-references"></a>Riferimenti dell'assembly

Per usare l'accesso tramite ADO.NET, è necessario aggiungere SQLite `System.Data` e `Mono.Data.Sqlite` fa riferimento al progetto iOS, come illustrato di seguito (per gli esempi in Visual Studio per Mac e Visual Studio):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Riferimenti ad assembly in Visual Studio per Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Riferimenti ad assembly in Visual Studio")

-----

Fare doppio clic su **riferimenti > Modifica i riferimenti...**  quindi fare clic per selezionare gli assembly necessari.

## <a name="about-monodatasqlite"></a>Sulle Mono.Data.Sqlite

Si userà il `Mono.Data.Sqlite.SqliteConnection` classe per creare un file di database vuoto e quindi creare un'istanza `SqliteCommand` gli oggetti che è possibile usare per eseguire istruzioni SQL sul database.


1. **Creazione di un Database vuoto** -chiamare le `CreateFile` metodo con un valore valido (ie. scrivibile) percorso del file. È necessario controllare se il file esiste già prima di chiamare questo metodo, in caso contrario, verrà creato un nuovo database (vuoto) nella parte superiore di quello precedente e i dati nel vecchio file andranno persi:

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > Il `dbPath` variabile deve essere determinata in base alla regole illustrate in precedenza in questo documento.

2. **Creazione di una connessione al Database** - dopo aver creato il file di database SQLite è possibile creare un oggetto di connessione per accedere ai dati. La connessione viene costruita con una stringa di connessione che assume la forma di `Data Source=file_path`, come illustrato di seguito:

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Come accennato in precedenza, una connessione non deve essere mai usata nuovamente in thread diversi. In caso di dubbio, creare la connessione in base alle esigenze e chiuderla quando l'operazione è terminata; Tuttavia, tenere in considerazione di eseguire spesso questa maggiore rispetto a quello richiesto troppo.
    
3. **Creazione ed esecuzione di un comando di Database** : dopo aver ottenuto una connessione è possibile eseguire i comandi SQL arbitrari su di esso. Il codice seguente illustra un'istruzione CREATE TABLE in esecuzione.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

Quando si esegue SQL direttamente nel database è consigliabile adottare le precauzioni normale non effettuare le richieste non valide, ad esempio si tenta di creare una tabella già esistente. Tenere traccia della struttura del database in modo che non si provochi un SqliteException, ad esempio "tabella degli errori di SQLite [elementi] esiste già".

## <a name="basic-data-access"></a>Accesso ai dati di base

Il *DataAccess_Basic* codice di esempio per questo documento aspetto simile al seguente quando si esegue iOS:

 ![](using-adonet-images/image9.png "esempio ADO.NET iOS")

Il codice seguente viene illustrato come eseguire semplici operazioni di SQLite e vengono illustrati i risultati in formato testo nella finestra principale dell'applicazione.

È necessario includere questi spazi dei nomi:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

Esempio di codice seguente mostra un'interazione intero database:

1.  Creazione del file di database
2.  Inserimento di alcuni dati
3.  Esecuzione di una query sui dati

Queste operazioni in genere potrebbero apparire in più posizioni in tutto il codice, ad esempio è possibile creare le tabelle e file di database al primo avvio dell'applicazione ed eseguire letture di dati e le scritture in singole schermate nell'app. Nell'esempio riportato di seguito sono state raggruppate in un singolo metodo per questo esempio:

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

Poiché SQLite consente arbitrari comandi SQL da eseguire sui dati, è possibile eseguire qualsiasi creare, inserire, aggiornare, eliminare o si desidera che le istruzioni SELECT. È possibile leggere sui comandi SQL supportati da SQLite in sito Web di Sqlite. Le istruzioni SQL vengono eseguite utilizzando uno dei tre metodi su un oggetto SqliteCommand:

-  **ExecuteNonQuery** : in genere usato per l'inserimento di dati o la creazione di tabella. Il valore restituito per alcune operazioni è il numero di righe interessate, in caso contrario è -1.
-  **ExecuteReader** – utilizzato quando una raccolta di righe deve essere restituita come un `SqlDataReader` .
-  **ExecuteScalar** : recupera un singolo valore (ad esempio una funzione di aggregazione).


### <a name="executenonquery"></a>EXECUTENONQUERY

Le istruzioni INSERT, UPDATE e DELETE restituirà il numero di righe interessate. Tutte le altre istruzioni SQL restituirà -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

Il metodo seguente viene illustrata una clausola WHERE nell'istruzione SELECT. Poiché il codice consiste nel creare un'istruzione SQL completa è necessario prestare attenzione per eseguire l'escape di caratteri riservati, ad esempio le virgolette (') in stringhe.

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

Il metodo ExecuteReader restituisce un oggetto SqliteDataReader. Oltre al metodo di lettura illustrato nell'esempio, includere altre proprietà utili:

-  **RowsAffected** : conteggio delle righe interessate dalla query.
-  **HasRows** : indica se tutte le righe sono state restituite.


### <a name="executescalar"></a>EXECUTESCALAR

Questa scheda per le istruzioni SELECT che restituiscono un singolo valore (ad esempio, una funzione di aggregazione).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Il `ExecuteScalar` è di tipo restituito del metodo `object` : è necessario il cast del risultato a seconda della query di database. Il risultato potrebbe essere un numero intero da una query di conteggio o una stringa da una query di selezione singola colonna. Si noti che ciò è diverso da altri metodi Execute che restituiscono un oggetto lettore o un conteggio del numero di righe interessate.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recipe di dati](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
