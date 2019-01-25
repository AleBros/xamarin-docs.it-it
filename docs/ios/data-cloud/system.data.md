---
title: System. Data in xamarin. IOS
description: Questo documento descrive come usare System. Data e Mono.Data.Sqlite.dll per accedere ai dati di SQLite in un'applicazione xamarin. IOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 014de47660f2c0ac8295495e417b3d5def135470
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879316"
---
# <a name="systemdata-in-xamarinios"></a>System. Data in xamarin. IOS

Aggiunge il supporto di xamarin. ios: 8.10 [System. Data](xref:System.Data), tra cui la `Mono.Data.Sqlite.dll` provider ADO.NET. Il supporto include l'aggiunta dei seguenti [assembly](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Esempio

Il programma seguente crea un database in `Documents/mydb.db3`, e se il database non esiste in precedenza, viene popolato con i dati di esempio. Il database viene quindi eseguita una query, con l'output scritto `stderr`.

### <a name="add-references"></a>Aggiungere i riferimenti

In primo luogo, fare clic sui **riferimenti** nodo, quindi scegliere **Modifica riferimenti...**  quindi selezionare `System.Data` e `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Aggiunta di nuovi riferimenti")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Codice di esempio

Il codice seguente illustra un semplice esempio di creazione di una tabella e l'inserimento di righe utilizzando i comandi SQL incorporati:

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
> Come indicato nell'esempio di codice precedente, è prassi non corretta a incorporare stringhe nei comandi SQL in quanto rende il codice vulnerabile agli [attacchi SQL injection](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Uso di parametri di comando

Il codice seguente viene illustrato come utilizzare i parametri di comando per inserire il testo immesso dall'utente in modo sicuro nel database (anche se il testo contiene caratteri speciali di SQL come singolo apostrofo):

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

Entrambe **System. Data** e **Mono.Data.Sqlite** mancano alcune funzionalità.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

Funzionalità non presente **System** è costituito da:

-  Qualsiasi elemento che richiede [System. CodeDom](xref:System.CodeDom) (ad esempio,  [System.Data.TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
-  Configurazione XML (ad esempio, supporto per file.  [System.Data.Common.DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
-   [System.Data.Common.DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (dipende dal supporto di file di configurazione XML)
-   [System.Data.OleDb](xref:System.Data.OleDb)
-   [System.Data.Odbc](xref:System.Data.Odbc)
-  Il `System.EnterpriseServices.dll` dipendenza è stata *rimosso* dalla `System.Data.dll` , con conseguente la rimozione del [SqlConnection.EnlistDistributedTransaction(ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) (metodo).


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Nel frattempo **Mono.Data.Sqlite.dll** ha subito alcuna modifica del codice sorgente, ma può essere host a un numero di *runtime* problemi dopo `Mono.Data.Sqlite.dll` associa SQLite 3.5. iOS 8, nel frattempo, viene fornito con SQLite 3.8.5. È sufficiente a dirsi, ha introdotto alcune modifiche tra le due versioni.

Versione precedente di iOS forniti con le versioni seguenti di SQLite:

- **iOS 7** -versione 3.7.13.
- **iOS 6** -versione 3.7.13.
- **iOS 5** -versione 3.7.7.
- **iOS 4** -versione 3.6.22.

I problemi più comuni vengono visualizzati come correlati a query di schema di database, ad esempio, che determina in fase di esecuzione sono disponibili colonne in una determinata tabella, ad esempio `Mono.Data.Sqlite.SqliteConnection.GetSchema` (eseguire l'override [DbConnection.GetSchema](xref:System.Data.Common.DbConnection.GetSchema) e `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` (viene sottoposto a override [DbDataReader.GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). In breve, sembra che qualsiasi valore usando [DataTable](xref:System.Data.DataTable) è improbabile che funzioni.

<a name="Data_Binding" />

## <a name="data-binding"></a>Data binding

Data Binding non è supportato in questo momento.

