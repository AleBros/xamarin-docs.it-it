---
title: System. Data in Xamarin.iOS
description: Questo documento descrive come usare System. Data e mono. Data. sqlite. dll per accedere ai dati SQLite in un'applicazione Xamarin.iOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: 4f5f6adf99306754fa7b2aa49855fe228e740d7e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016941"
---
# <a name="systemdata-in-xamarinios"></a>System. Data in Xamarin.iOS

Xamarin.iOS 8,10 aggiunge il supporto per [System. Data](xref:System.Data), incluso il provider di `Mono.Data.Sqlite.dll` ADO.NET. Il supporto include l'aggiunta degli [assembly](~/cross-platform/internals/available-assemblies.md)seguenti:

- `System.Data.dll`
- `System.Data.Service.Client.dll`
- `System.Transactions.dll`
- `Mono.Data.Tds.dll`
- `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Esempio

Il seguente programma crea un database in `Documents/mydb.db3`e, se il database non esiste in precedenza, viene popolato con dati di esempio. Viene quindi eseguita una query sul database, con l'output scritto nel `stderr`.

### <a name="add-references"></a>Aggiungi riferimenti

In primo luogo, fare clic con il pulsante destro del mouse sul nodo **riferimenti** e scegliere **modifica riferimenti** , quindi selezionare `System.Data` e `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Adding new references")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Codice di esempio

Il codice seguente illustra un semplice esempio di creazione di una tabella e di inserimento di righe con comandi SQL incorporati:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> Come indicato nell'esempio di codice precedente, è consigliabile incorporare le stringhe nei comandi SQL perché rende il codice vulnerabile a [SQL injection](https://en.wikipedia.org/wiki/SQL_injection).

### <a name="using-command-parameters"></a>Uso di parametri di comando

Il codice seguente illustra come usare i parametri di comando per inserire in modo sicuro il testo immesso dall'utente nel database (anche se il testo contiene caratteri SQL speciali come un apostrofo singolo):

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>Funzionalità mancanti

In **System. Data** e **mono. Data. sqlite** mancano alcune funzionalità.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

La funzionalità mancante da **System. Data. dll** è costituita da:

- Qualsiasi elemento che richiede [System. CodeDom](xref:System.CodeDom) , ad esempio  [System. Data. TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
- Supporto file di configurazione XML (ad esempio  [System. Data. Common. DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
- [System. Data. Common. DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (dipende dal supporto per i file di configurazione XML)
- [System. Data. OleDb](xref:System.Data.OleDb)
- [System. Data. ODBC](xref:System.Data.Odbc)
- La dipendenza `System.EnterpriseServices.dll` è stata *rimossa* da `System.Data.dll`, causando la rimozione del metodo [SqlConnection. EnlistDistributedTransaction (ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) .

<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono. Data. sqlite

Nel frattempo, **mono. Data. sqlite. dll** non ha avuto alcuna modifica al codice sorgente, ma potrebbe essere host a una serie di problemi di *Runtime* poiché `Mono.Data.Sqlite.dll` associa SQLite 3,5. iOS 8, nel frattempo, viene fornito con SQLite 3.8.5. Basti dire che alcune delle due versioni sono state modificate.

Versione precedente di iOS Ship con le versioni seguenti di SQLite:

- **iOS 7** -versione 3.7.13.
- **iOS 6** -versione 3.7.13.
- **iOS 5** -versione 3.7.7.
- **iOS 4** versione 3.6.22.

I problemi più comuni sembrano essere correlati all'esecuzione di query sullo schema del database, ad esempio determinare in fase di esecuzione le colonne presenti in una determinata tabella, ad esempio `Mono.Data.Sqlite.SqliteConnection.GetSchema` (override di [DbConnection. GetSchema](xref:System.Data.Common.DbConnection.GetSchema) e `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` (override [ DbDataReader. GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). In breve, sembra che tutto ciò che usa [DataTable](xref:System.Data.DataTable) sia improbabile.

<a name="Data_Binding" />

## <a name="data-binding"></a>Data binding

Il data binding non è supportato in questo momento.
