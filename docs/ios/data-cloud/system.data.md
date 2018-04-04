---
title: System.Data
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b141dfac49e2cfa2dc80b7c0e4ca3a93968590a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="systemdata"></a>System.Data

Aggiunge il supporto per xamarin 8.10 [System. Data](https://developer.xamarin.com/api/namespace/System.Data/), tra cui la `Mono.Data.Sqlite.dll` provider ADO.NET. Il supporto include l'aggiunta delle seguenti [assembly](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`


<a name="Example" />

## <a name="example"></a>Esempio

Il seguente programma crea un database in `Documents/mydb.db3`, e se il database non esiste in precedenza, viene popolata con dati di esempio. Il database viene quindi eseguita una query, con l'output scritto `stderr`.

### <a name="add-references"></a>Aggiungere riferimenti

In primo luogo, fare clic su di **riferimenti** nodo e scegliere **Modifica riferimenti...**  selezionare `System.Data` e `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Aggiunta di nuovi riferimenti")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Codice di esempio

Il codice seguente viene illustrato un semplice esempio di creazione di una tabella e l'inserimento di righe utilizzando i comandi SQL incorporati:

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
> Come indicato nell'esempio di codice precedente, è una prassi per incorporare le stringhe nei comandi SQL perché il codice rende vulnerabile agli [attacchi SQL injection](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Utilizzo di parametri di comando

Il codice seguente viene illustrato come utilizzare i parametri del comando per inserire il testo immesso dall'utente in modo sicuro nel database (anche se il testo contiene caratteri speciali di SQL come singolo apostrofo):

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

Entrambi **System. Data** e **Mono.Data.Sqlite** mancano alcune funzionalità.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

La funzionalità mancante da **System.Data.dll** è costituito da:

-  Qualsiasi elemento che richiedono [System. CodeDom](https://developer.xamarin.com/api/namespace/System.CodeDom/) (ad esempio,  [System.Data.TypedDataSetGenerator](https://developer.xamarin.com/api/type/System.Data.TypedDataSetGenerator/) )
-  Configurazione XML (ad esempio, supporto per file.  [System.Data.Common.DbProviderConfigurationHandler](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderConfigurationHandler/) )
-   [System.Data.Common.DbProviderFactories](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderFactories/) (dipende dal supporto di file di configurazione XML)
-   [System.Data.OleDb](https://developer.xamarin.com/api/namespace/System.Data.OleDb/)
-   [System.Data.Odbc](https://developer.xamarin.com/api/namespace/System.Data.Odbc/)
-  Il `System.EnterpriseServices.dll` dipendenza è *rimosso* da `System.Data.dll` , comporta la rimozione del [SqlConnection.EnlistDistributedTransaction(ITransaction)](https://developer.xamarin.com/api/member/System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction/(System.EnterpriseServices.ITransaction)) metodo.


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Nel frattempo, **Mono.Data.Sqlite.dll** non subito alcuna modifica del codice sorgente, ma potrebbe essere un numero di host *runtime* problemi dal `Mono.Data.Sqlite.dll` associa SQLite 3.5. Nel frattempo, 8, iOS dotato di SQLite 3.8.5. Basti a dirsi, alcuni elementi sono stati modificati tra le due versioni.

La versione precedente di iOS forniti con le versioni seguenti di SQLite:

- **iOS 7** -versione 3.7.13.
- **iOS 6** -versione 3.7.13.
- **iOS 5** -versione 3.7.7.
- **iOS 4** -versione 3.6.22.

I problemi più comuni sembrano essere correlato a una query dello schema di database, ad esempio, determinare in fase di esecuzione quali colonne sono presenti in una determinata tabella, ad esempio `Mono.Data.Sqlite.SqliteConnection.GetSchema` (override [DbConnection.GetSchema](https://developer.xamarin.com/api/member/System.Data.Common.DbConnection.GetSchema/)) e `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` ( si esegue l'override [DbDataReader.GetSchemaTable](https://developer.xamarin.com/api/member/System.Data.Common.DbDataReader.GetSchemaTable/)). In breve, sembra che qualsiasi valore diverso tramita [DataTable](https://developer.xamarin.com/api/type/System.Data.DataTable/) è improbabile che funzioni.

<a name="Data_Binding" />

## <a name="data-binding"></a>Data binding

Associazione di dati non è supportata in questo momento.

