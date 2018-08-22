---
title: Database in xamarin. Mac
description: Questo articolo viene illustrato l'utilizzo chiave-valore di codifica e chiave-valore osservando la possibilità di associazione dati tra i database SQLite e gli elementi dell'interfaccia utente in Interface Builder di Xcode. Viene anche illustrato l'uso di SQLite.NET ORM per fornire accesso ai dati di SQLite.
ms.prod: xamarin
ms.assetid: 44FAFDA8-612A-4E0F-8BB4-5C92A3F4D552
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: db418df0869d73e9f04982fb508fd261304240c0
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251214"
---
# <a name="databases-in-xamarinmac"></a>Database in xamarin. Mac

_Questo articolo viene illustrato l'utilizzo chiave-valore di codifica e chiave-valore osservando la possibilità di associazione dati tra i database SQLite e gli elementi dell'interfaccia utente in Interface Builder di Xcode. Viene anche illustrato l'uso di SQLite.NET ORM per fornire accesso ai dati di SQLite._

## <a name="overview"></a>Panoramica

Quando si usa c# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare gli stessi database SQLite che possa accedere a un'applicazione xamarin. IOS o xamarin. Android.

In questo articolo è verranno illustrati due modi per accedere ai dati di SQLite:

1. **Accesso diretto** : accedendo direttamente un SQLite Database, è possibile usare dati dal database per la codifica di chiave-valore e del data binding con elementi dell'interfaccia utente creati in Interface Builder di Xcode. Usando chiave-valore di codifica e di data binding tecniche nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È anche il vantaggio di un'ulteriore separazione dei dati di backup (_modello di dati_) dal front end interfaccia utente (_Model-View-Controller_), sfociando in più facilmente gestibile, più flessibile dell'applicazione progettazione.
2. **SQLite.NET ORM** : tramite open source [SQLite.NET](http://www.sqlite.org) relazione Manager ORM (Object) è possibile ridurre notevolmente la quantità di codice necessario per leggere e scrivere dati da un database SQLite. Questo dati possono quindi essere usati per popolare un elemento dell'interfaccia utente, ad esempio una visualizzazione tabella.

[![Un esempio di app in esecuzione](databases-images/intro01.png "un esempio di app in esecuzione")](databases-images/intro01-large.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di chiave-valore di codifica e il data binding con i database SQLite in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Poiché si userà la codifica di chiave-valore e il data binding, collaborare tramite il [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) in primo luogo, come le tecniche di base e verranno trattati i concetti che verrà usato in questa documentazione e relativo esempio applicazione.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` attributi utilizzato per impostare backup di classi c# per gli oggetti di Objective-C e interfaccia utente di elementi.

## <a name="direct-sqlite-access"></a>Accesso diretto di SQLite

Per i dati di SQLite che sta per essere associato a elementi dell'interfaccia utente in Interface Builder di Xcode, è altamente consigliabile che si accede al database SQLite direttamente (invece di usare una tecnica, ad esempio un ORM), poiché si ha il controllo totale sulla modalità dei dati vengono scritti e letti  dal database.

Come abbiamo visto nel [Data Binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione, con chiave-valore di codifica e i dati tecniche di associazione nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e Consente di gestire per popolare e lavorare con gli elementi dell'interfaccia utente. Se combinato con accesso diretto a un database SQLite, è possibile ridurre anche notevolmente la quantità di codice necessario per leggere e scrivere dati a tale database.

In questo articolo, verrà modificata l'app di esempio di associazione dati e la documentazione di scrittura di codice chiave-valore per utilizzare un SQLite Database come origine il backup per l'associazione.

### <a name="including-sqlite-database-support"></a>Incluso il supporto di database SQLite

Prima di continuare, è necessario aggiungere il supporto di database SQLite all'applicazione tramite l'inclusione di riferimenti a un paio di. File DLL.

Seguire questa procedura:

1. Nel **riquadro della soluzione**, fare clic sui **riferimenti** cartella e selezionare **Modifica riferimenti**.
2. Selezionare sia la **Mono.Data.Sqlite** e **System. Data** assembly: 

    [![Aggiunta dei riferimenti necessari](databases-images/reference01.png "aggiungendo i riferimenti necessari")](databases-images/reference01-large.png#lightbox)
3. Scegliere il **OK** pulsante per salvare le modifiche e aggiungere i riferimenti.

### <a name="modifying-the-data-model"></a>Modifica il modello di dati

Ora che è stato aggiunto supporto per accedere direttamente a un database SQLite per la nostra applicazione, è necessario modificare l'oggetto modello di dati per leggere e scrivere i dati dal database (nonché offrire chiave-valore di codifica e il data binding). Nel caso di applicazione di esempio, si modificherà il **PersonModel.cs** classe e renderlo simile al seguente:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _ID = "";
        private string _managerID = "";
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        private SqliteConnection _conn = null;
        #endregion

        #region Computed Properties
        public SqliteConnection Conn {
            get { return _conn; }
            set { _conn = value; }
        }

        [Export("ID")]
        public string ID {
            get { return _ID; }
            set {
                WillChangeValue ("ID");
                _ID = value;
                DidChangeValue ("ID");
            }
        }

        [Export("ManagerID")]
        public string ManagerID {
            get { return _managerID; }
            set {
                WillChangeValue ("ManagerID");
                _managerID = value;
                DidChangeValue ("ManagerID");
            }
        }

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }

        public PersonModel (string id, string name, string occupation)
        {
            // Initialize
            this.ID = id;
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (SqliteConnection conn, string id)
        {
            // Load from database
            Load (conn, id);
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion

        #region SQLite Routines
        public void Create(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Create new record ID?
            if (ID == "") {
                ID = Guid.NewGuid ().ToString();
            }

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);
                command.Parameters.AddWithValue ("@COL2", Name);
                command.Parameters.AddWithValue ("@COL3", Occupation);
                command.Parameters.AddWithValue ("@COL4", isManager);
                command.Parameters.AddWithValue ("@COL5", ManagerID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Save children to database as well
            for (nuint n = 0; n < People.Count; ++n) {
                // Grab person
                var Person = People.GetItem<PersonModel>(n);

                // Save manager ID and create the sub record
                Person.ManagerID = ID;
                Person.Create (conn);
            }

            // Save last connection
            _conn = conn;
        }

        public void Update(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);
                command.Parameters.AddWithValue ("@COL2", Name);
                command.Parameters.AddWithValue ("@COL3", Occupation);
                command.Parameters.AddWithValue ("@COL4", isManager);
                command.Parameters.AddWithValue ("@COL5", ManagerID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Save children to database as well
            for (nuint n = 0; n < People.Count; ++n) {
                // Grab person
                var Person = People.GetItem<PersonModel>(n);

                // Update sub record
                Person.Update (conn);
            }

            // Save last connection
            _conn = conn;
        }

        public void Load(SqliteConnection conn, string id) {
            bool shouldClose = false;

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Is the database already open?
            if (conn.State != ConnectionState.Open) {
                shouldClose = true;
                conn.Open ();
            }

            // Execute query
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "SELECT * FROM [People] WHERE ID = @COL1";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", id);

                using (var reader = command.ExecuteReader ()) {
                    while (reader.Read ()) {
                        // Pull values back into class
                        ID = (string)reader [0];
                        Name = (string)reader [1];
                        Occupation = (string)reader [2];
                        isManager = (bool)reader [3];
                        ManagerID = (string)reader [4];
                    }
                }
            }

            // Is this a manager?
            if (isManager) {
                // Yes, load children
                using (var command = conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

                    // Populate with data from the record
                    command.Parameters.AddWithValue ("@COL1", id);

                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Load child and add to collection
                            var childID = (string)reader [0];
                            var person = new PersonModel (conn, childID);
                            _people.Add (person);
                        }
                    }
                }
            }

            // Should we close the connection to the database
            if (shouldClose) {
                conn.Close ();
            }

            // Save last connection
            _conn = conn;
        }

        public void Delete(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Empty class
            ID = "";
            ManagerID = "";
            Name = "";
            Occupation = "";
            isManager = false;
            _people = new NSMutableArray();

            // Save last connection
            _conn = conn;
        }
        #endregion
    }
}
```

Diamo un'occhiata le modifiche in dettaglio di seguito.

In primo luogo, sono state aggiunte alcune istruzioni using che sono necessari per l'uso di SQLite e abbiamo aggiunto una variabile in cui salvare l'ultima connessione al database SQLite:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection _conn = null;
```

Si userà questa connessione salvata per salvare automaticamente tutte le modifiche al record nel database quando l'utente modifica il contenuto nell'interfaccia utente tramite l'associazione dati:

```csharp
[Export("Name")]
public string Name {
    get { return _name; }
    set {
        WillChangeValue ("Name");
        _name = value;
        DidChangeValue ("Name");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}

[Export("Occupation")]
public string Occupation {
    get { return _occupation; }
    set {
        WillChangeValue ("Occupation");
        _occupation = value;
        DidChangeValue ("Occupation");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}

[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}
```

Eventuali modifiche apportate al **nome**, **Occupation** oppure **isManager** proprietà verranno inviate al database se i dati non sono stati salvati non esiste (ad esempio, se il `_conn` la variabile non è `null`). Successivamente, verrà ora esaminato i metodi che è stata aggiunta al **Create**, **Update**, **carico** e **Elimina** utenti dal database.

#### <a name="create-a-new-record"></a>Creare un nuovo record

Per creare un nuovo record nel database SQLite è stato aggiunto il codice seguente:

```csharp
public void Create(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Create new record ID?
    if (ID == "") {
        ID = Guid.NewGuid ().ToString();
    }

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);
        command.Parameters.AddWithValue ("@COL2", Name);
        command.Parameters.AddWithValue ("@COL3", Occupation);
        command.Parameters.AddWithValue ("@COL4", isManager);
        command.Parameters.AddWithValue ("@COL5", ManagerID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Save children to database as well
    for (nuint n = 0; n < People.Count; ++n) {
        // Grab person
        var Person = People.GetItem<PersonModel>(n);

        // Save manager ID and create the sub record
        Person.ManagerID = ID;
        Person.Create (conn);
    }

    // Save last connection
    _conn = conn;
}
```

Si sta usando un `SQLiteCommand` per creare il nuovo record nel database. Si ottiene un nuovo comando dal `SQLiteConnection` (conn) che viene passato al metodo chiamando `CreateCommand`. Successivamente, impostiamo l'istruzione SQL per la scrittura del nuovo record, fornendo i parametri per i valori effettivi:

```csharp
command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";
```

In un secondo momento è impostare i valori per i parametri usando il `Parameters.AddWithValue` metodo su di `SQLiteCommand`. Usando i parametri, si assicura che i valori (ad esempio una virgoletta singola) vengono codificati correttamente prima dell'invio di SQLite. Esempio:

```csharp
command.Parameters.AddWithValue ("@COL1", ID);
```

Infine, poiché una persona può essere una gestione e dispone di una raccolta di dipendenti interno di esse, siamo in modo ricorsivo la chiamata di `Create` metodo su questi utenti per salvarli anche nel database:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Save manager ID and create the sub record
    Person.ManagerID = ID;
    Person.Create (conn);
}
```

#### <a name="updating-a-record"></a>Aggiornamento di un record

Per aggiornare un record esistente nel database SQLite è stato aggiunto il codice seguente:

```csharp
public void Update(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);
        command.Parameters.AddWithValue ("@COL2", Name);
        command.Parameters.AddWithValue ("@COL3", Occupation);
        command.Parameters.AddWithValue ("@COL4", isManager);
        command.Parameters.AddWithValue ("@COL5", ManagerID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Save children to database as well
    for (nuint n = 0; n < People.Count; ++n) {
        // Grab person
        var Person = People.GetItem<PersonModel>(n);

        // Update sub record
        Person.Update (conn);
    }

    // Save last connection
    _conn = conn;
}
```

Ad esempio **Create** sopra, si ottiene una `SQLiteCommand` dall'oggetto passato in `SQLiteConnection`e impostare il SQL per aggiornare i record (specificando i parametri):

```csharp
command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";
```

È riempire i valori dei parametri (esempio: `command.Parameters.AddWithValue ("@COL1", ID);`) e anche in questo caso, chiamare in modo ricorsivo aggiornamento su qualsiasi elemento figlio record:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Update sub record
    Person.Update (conn);
}
```
#### <a name="loading-a-record"></a>Il caricamento di un record

Per caricare un record esistente dal database SQLite è stato aggiunto il codice seguente:

```csharp
public void Load(SqliteConnection conn, string id) {
    bool shouldClose = false;

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Is the database already open?
    if (conn.State != ConnectionState.Open) {
        shouldClose = true;
        conn.Open ();
    }

    // Execute query
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "SELECT * FROM [People] WHERE ID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", id);

        using (var reader = command.ExecuteReader ()) {
            while (reader.Read ()) {
                // Pull values back into class
                ID = (string)reader [0];
                Name = (string)reader [1];
                Occupation = (string)reader [2];
                isManager = (bool)reader [3];
                ManagerID = (string)reader [4];
            }
        }
    }

    // Is this a manager?
    if (isManager) {
        // Yes, load children
        using (var command = conn.CreateCommand ()) {
            // Create new command
            command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

            // Populate with data from the record
            command.Parameters.AddWithValue ("@COL1", id);

            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Load child and add to collection
                    var childID = (string)reader [0];
                    var person = new PersonModel (conn, childID);
                    _people.Add (person);
                }
            }
        }
    }

    // Should we close the connection to the database
    if (shouldClose) {
        conn.Close ();
    }

    // Save last connection
    _conn = conn;
}
```

Perché la routine può essere chiamata in modo ricorsivo da un oggetto padre (ad esempio, il caricamento dell'oggetto relativi ai dipendenti di un oggetto di gestione), è stato aggiunto un codice speciale per gestire l'apertura e chiusura della connessione al database:

```csharp
bool shouldClose = false;
...

// Is the database already open?
if (conn.State != ConnectionState.Open) {
    shouldClose = true;
    conn.Open ();
}
...

// Should we close the connection to the database
if (shouldClose) {
    conn.Close ();
}

```

Come sempre, impostiamo il SQL per recuperare il record e usare i parametri:

```csharp
// Create new command
command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", id);
```

Infine, utilizziamo un lettore di dati per eseguire la query e restituire i campi di record (che copiamo nell'istanza di `PersonModel` classe):

```csharp
using (var reader = command.ExecuteReader ()) {
    while (reader.Read ()) {
        // Pull values back into class
        ID = (string)reader [0];
        Name = (string)reader [1];
        Occupation = (string)reader [2];
        isManager = (bool)reader [3];
        ManagerID = (string)reader [4];
    }
}
```

Se questa persona è un manager, è necessario anche caricare tutti i dipendenti (anche in questo caso chiamando il metodo in modo ricorsivo i `Load` (metodo)):

```csharp
// Is this a manager?
if (isManager) {
    // Yes, load children
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", id);

        using (var reader = command.ExecuteReader ()) {
            while (reader.Read ()) {
                // Load child and add to collection
                var childID = (string)reader [0];
                var person = new PersonModel (conn, childID);
                _people.Add (person);
            }
        }
    }
}
```

#### <a name="deleting-a-record"></a>Eliminazione di un record

Per eliminare un record esistente dal database SQLite è stato aggiunto il codice seguente:

```csharp
public void Delete(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Empty class
    ID = "";
    ManagerID = "";
    Name = "";
    Occupation = "";
    isManager = false;
    _people = new NSMutableArray();

    // Save last connection
    _conn = conn;
}
```

Di seguito è riportato il codice SQL per eliminare il record di responsabili e i record di tutti i dipendenti con tale gestione (con parametri):

```csharp
// Create new command
command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", ID);
```

Dopo aver rimosso il record, si cancellerà l'istanza corrente del `PersonModel` classe:

```csharp
// Empty class
ID = "";
ManagerID = "";
Name = "";
Occupation = "";
isManager = false;
_people = new NSMutableArray();
```

### <a name="initializing-the-database"></a>L'inizializzazione del database

Con le modifiche al nostro modello di dati presenti per supportare la lettura e scrittura al database, è necessario aprire una connessione al database e inizializzarlo alla prima esecuzione. Aggiungere il codice seguente al nostro **MainWindow.cs** file:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection DatabaseConnection = null;
...

private SqliteConnection GetDatabaseConnection() {
    var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
    string db = Path.Combine (documents, "People.db3");

    // Create the database if it doesn't already exist
    bool exists = File.Exists (db);
    if (!exists)
        SqliteConnection.CreateFile (db);

    // Create connection to the database
    var conn = new SqliteConnection("Data Source=" + db);

    // Set the structure of the database
    if (!exists) {
        var commands = new[] {
            "CREATE TABLE People (ID TEXT, Name TEXT, Occupation TEXT, isManager BOOLEAN, ManagerID TEXT)"
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

        // Build list of employees
        var Craig = new PersonModel ("0","Craig Dunn", "Documentation Manager");
        Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
        Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
        Craig.Create (conn);

        var Larry = new PersonModel ("1","Larry O'Brien", "API Documentation Manager");
        Larry.AddPerson (new PersonModel ("Mike Norman", "API Documentor"));
        Larry.Create (conn);
    }

    // Return new connection
    return conn;
}
```

Diamo uno sguardo al codice precedente. Prima di tutto è selezionare un percorso per il nuovo database (in questo esempio, il Desktop dell'utente), verificare se il database esiste e in caso contrario, crearla:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "People.db3");

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
if (!exists)
    SqliteConnection.CreateFile (db);
```

Successivamente, si stabilisce la connessione al database usando il percorso creato in precedenza:

```csharp
var conn = new SqliteConnection("Data Source=" + db);
```

Creiamo quindi tutte le tabelle SQL nel database che è necessario:

```csharp
var commands = new[] {
    "CREATE TABLE People (ID TEXT, Name TEXT, Occupation TEXT, isManager BOOLEAN, ManagerID TEXT)"
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
```

Infine, si usa il nostro modello di dati (`PersonModel`) per creare un set predefinito di record per il database la prima volta l'applicazione viene eseguita o se il database risulterà mancante:

```csharp
// Build list of employees
var Craig = new PersonModel ("0","Craig Dunn", "Documentation Manager");
Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
Craig.Create (conn);

var Larry = new PersonModel ("1","Larry O'Brien", "API Documentation Manager");
Larry.AddPerson (new PersonModel ("Mike Norman", "API Documentor"));
Larry.Create (conn);
``` 

Quando l'applicazione viene avviato e apre la finestra principale, è stabilire una connessione al database usando il codice che aggiunto in precedenza:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get access to database
    DatabaseConnection = GetDatabaseConnection ();
}
```

### <a name="loading-bound-data"></a>Caricamento dei dati associati

Tutti i componenti per accedere direttamente ai dati associati da un database SQLite posto, è possibile caricare i dati nelle diverse visualizzazioni che fornisce la nostra applicazione e verrà visualizzato automaticamente nella nostra interfaccia utente.

#### <a name="loading-a-single-record"></a>Il caricamento di un singolo record

Per caricare un singolo record in cui l'ID è noto, è possibile usare il codice seguente:

```csharp
Person = new PersonModel (Conn, "0");
```

#### <a name="loading-all-records"></a>Il caricamento di tutti i record

Per caricare tutte le persone, indipendentemente dal fatto che se un gestore che siano o meno, usare il codice seguente:

```csharp
// Load all employees
_conn.Open ();
using (var command = _conn.CreateCommand ()) {
    // Create new command
    command.CommandText = "SELECT ID FROM [People]";

    using (var reader = command.ExecuteReader ()) {
        while (reader.Read ()) {
            // Load child and add to collection
            var childID = (string)reader [0];
            var person = new PersonModel (_conn, childID);
            AddPerson (person);
        }
    }
}
_conn.Close ();

```

In questo caso, viene usato un overload del costruttore per il `PersonModel` classe per la persona che viene caricato in memoria:

```csharp
var person = new PersonModel (_conn, childID);
```

Qui viene chiamato anche la classe associato a dati per aggiungere la persona a nostra raccolta di persone `AddPerson (person)`, in questo modo l'interfaccia utente riconosca la modifica e lo visualizza:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}
```

#### <a name="loading-top-level-records-only"></a>Il caricamento solo i record di livello superiore

Per caricare solo i Manager (ad esempio, per visualizzare i dati in una visualizzazione struttura), utilizziamo il codice seguente:

```csharp
// Load only managers employees
_conn.Open ();
using (var command = _conn.CreateCommand ()) {
    // Create new command
    command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1";

    using (var reader = command.ExecuteReader ()) {
        while (reader.Read ()) {
            // Load child and add to collection
            var childID = (string)reader [0];
            var person = new PersonModel (_conn, childID);
            AddPerson (person);
        }
    }
}
_conn.Close ();
```

L'unica vera differenza nell'istruzione SQL in (che carica solo i Manager `command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1"`), ma funziona esattamente come la sezione precedente in caso contrario.

<a name="Databases-and-ComboBoxes" />

### <a name="databases-and-comboboxes"></a>I database e caselle combinate

I controlli Menu disponibile per macOS (ad esempio, la casella combinata) può essere impostati su popolare l'elenco a discesa da un elenco interno (che può essere preventivamente definito in Interface Builder o popolato mediante codice) oppure specificando il proprio origine dati esterna e personalizzati. Visualizzare [fornendo i dati di controllo Menu](~/mac/user-interface/standard-controls.md#Providing-Menu-Control-Data) per altri dettagli.

Ad esempio, modificare l'esempio di associazione semplice in precedenza in Interface Builder, aggiungere una casella combinata ed esporlo tramite un outlet denominato `EmployeeSelector`:

[![Esposizione di un negozio di casella combinata](databases-images/combo01.png "esponendo una presa di casella combinata")](databases-images/combo01-large.png#lightbox)

Nel **Attributes Inspector**, selezionare il **durante la digitazione** e **Zdroj dat utilizza** proprietà:

![Configurare gli attributi di casella combinata](databases-images/combo02.png "configurazione gli attributi di casella combinata")

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

#### <a name="providing-combobox-data"></a>Fornisce dati del controllo combobox

Successivamente, aggiungere una nuova classe al progetto denominato `ComboBoxDataSource` e renderlo simile al seguente:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    public class ComboBoxDataSource : NSComboBoxDataSource
    {
        #region Private Variables
        private SqliteConnection _conn = null;
        private string _tableName = "";
        private string _IDField = "ID";
        private string _displayField = "";
        private nint _recordCount = 0;
        #endregion

        #region Computed Properties
        public SqliteConnection Conn {
            get { return _conn; }
            set { _conn = value; }
        }

        public string TableName {
            get { return _tableName; }
            set { 
                _tableName = value;
                _recordCount = GetRecordCount ();
            }
        }

        public string IDField {
            get { return _IDField; }
            set {
                _IDField = value; 
                _recordCount = GetRecordCount ();
            }
        }

        public string DisplayField {
            get { return _displayField; }
            set { 
                _displayField = value; 
                _recordCount = GetRecordCount ();
            }
        }

        public nint RecordCount {
            get { return _recordCount; }
        }
        #endregion

        #region Constructors
        public ComboBoxDataSource (SqliteConnection conn, string tableName, string displayField)
        {
            // Initialize
            this.Conn = conn;
            this.TableName = tableName;
            this.DisplayField = displayField;
        }

        public ComboBoxDataSource (SqliteConnection conn, string tableName, string idField, string displayField)
        {
            // Initialize
            this.Conn = conn;
            this.TableName = tableName;
            this.IDField = idField;
            this.DisplayField = displayField;
        }
        #endregion

        #region Private Methods
        private nint GetRecordCount ()
        {
            bool shouldClose = false;
            nint count = 0;

            // Has a Table, ID and display field been specified?
            if (TableName !="" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT count({IDField}) FROM [{TableName}]";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read count from query
                            var result = (long)reader [0];
                            count = (nint)result;
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return the number of records
            return count;
        }
        #endregion

        #region Public Methods
        public string IDForIndex (nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {IDField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public string ValueForIndex (nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public string IDForValue (string value)
        {
            NSString result = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {IDField} FROM [{TableName}] WHERE {DisplayField} = @VAL";

                    // Populate parameters
                    command.Parameters.AddWithValue ("@VAL", value);

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            result = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return result;
        }
        #endregion 

        #region Override Methods
        public override nint ItemCount (NSComboBox comboBox)
        {
            return RecordCount;
        }

        public override NSObject ObjectValueForItem (NSComboBox comboBox, nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public override nint IndexOfItem (NSComboBox comboBox, string value)
        {
            bool shouldClose = false;
            bool found = false;
            string field = "";
            nint index = NSRange.NotFound;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read () && !found) {
                            // Read the display field from the query
                            field = (string)reader [0];
                            ++index;

                            // Is this the value we are searching for?
                            if (value == field) {
                                // Yes, exit loop
                                found = true;
                            }
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return index;
        }

        public override string CompletedString (NSComboBox comboBox, string uncompletedString)
        {
            bool shouldClose = false;
            bool found = false;
            string field = "";

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Escape search string
                uncompletedString = uncompletedString.Replace ("'", "");

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] WHERE {DisplayField} LIKE @VAL";

                    // Populate parameters
                    command.Parameters.AddWithValue ("@VAL", uncompletedString + "%");

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            field = (string)reader [0];
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return field;
        }
        #endregion
    }
}
```

In questo esempio viene creato un nuovo `NSComboBoxDataSource` che può presentare gli elementi di casella combinata da qualsiasi origine dati di SQLite. In primo luogo, definiamo le proprietà seguenti:

- `Conn` : Ottiene o imposta una connessione al database SQLite.
- `TableName` : Ottiene o imposta il nome della tabella.
- `IDField` : Ottiene o imposta il campo che rappresenta l'ID univoco per la tabella specificata. Il valore predefinito è `ID`.
- `DisplayField` : Ottiene o imposta il campo che viene visualizzato nell'elenco a discesa.
- `RecordCount` -Ottiene il numero di record nella tabella specificata.

Quando si crea una nuova istanza dell'oggetto, è passare la connessione, nome della tabella, facoltativamente, il campo ID e il campo di visualizzazione:

```csharp
public ComboBoxDataSource (SqliteConnection conn, string tableName, string displayField)
{
    // Initialize
    this.Conn = conn;
    this.TableName = tableName;
    this.DisplayField = displayField;
}
```

Il `GetRecordCount` metodo restituisce il numero di record nella tabella specificata:

```csharp
private nint GetRecordCount ()
{
    bool shouldClose = false;
    nint count = 0;

    // Has a Table, ID and display field been specified?
    if (TableName !="" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT count({IDField}) FROM [{TableName}]";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read count from query
                    var result = (long)reader [0];
                    count = (nint)result;
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return the number of records
    return count;
}
```

Viene chiamato ogni volta che il `TableName`, `IDField` o `DisplayField` valore della proprietà viene modificato.

Il `IDForIndex` metodo restituisce l'ID univoco (`IDField`) per il record in corrispondenza dell'indice di elemento di elenco a discesa specificato: 

```csharp
public string IDForIndex (nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {IDField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

Il `ValueForIndex` metodo viene restituito il valore (`DisplayField`) per l'elemento in corrispondenza dell'indice di elenco a discesa specificato:

```csharp
public string ValueForIndex (nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

Il `IDForValue` metodo restituisce l'ID univoco (`IDField`) per il valore specificato (`DisplayField`):

```csharp
public string IDForValue (string value)
{
    NSString result = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {IDField} FROM [{TableName}] WHERE {DisplayField} = @VAL";

            // Populate parameters
            command.Parameters.AddWithValue ("@VAL", value);

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    result = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return result;
}
```

Il `ItemCount` restituisce il numero di pre-calcolato di elementi nell'elenco come calcolato quando il `TableName`, `IDField` o `DisplayField` vengono modificate le proprietà:

```csharp
public override nint ItemCount (NSComboBox comboBox)
{
    return RecordCount;
}
```

Il `ObjectValueForItem` metodo fornisce il valore (`DisplayField`) per l'indice dell'elemento elenco a discesa specificato:

```csharp
public override NSObject ObjectValueForItem (NSComboBox comboBox, nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

Si noti che si sta usando il `LIMIT` e `OFFSET` istruzioni nel nostro comando SQLite per limitare al un record corso dovevamo.

Il `IndexOfItem` metodo restituisce l'indice dell'elemento elenco a discesa del valore (`DisplayField`) specificato:

```csharp
public override nint IndexOfItem (NSComboBox comboBox, string value)
{
    bool shouldClose = false;
    bool found = false;
    string field = "";
    nint index = NSRange.NotFound;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read () && !found) {
                    // Read the display field from the query
                    field = (string)reader [0];
                    ++index;

                    // Is this the value we are searching for?
                    if (value == field) {
                        // Yes, exit loop
                        found = true;
                    }
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return index;
}
```

Se il valore non viene trovato, il `NSRange.NotFound` valore restituito e vengono deselezionati tutti gli elementi dall'elenco a discesa.

Il `CompletedString` metodo viene restituito il primo valore corrispondente (`DisplayField`) per una voce parzialmente tipizzata:

```csharp
public override string CompletedString (NSComboBox comboBox, string uncompletedString)
{
    bool shouldClose = false;
    bool found = false;
    string field = "";

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Escape search string
        uncompletedString = uncompletedString.Replace ("'", "");

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] WHERE {DisplayField} LIKE @VAL";

            // Populate parameters
            command.Parameters.AddWithValue ("@VAL", uncompletedString + "%");

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    field = (string)reader [0];
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return field;
}
```

#### <a name="displaying-data-and-responding-to-events"></a>Visualizzazione dei dati e risposta agli eventi

Per riunire tutti i componenti correlati, modificare il `SubviewSimpleBindingController` e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    public partial class SubviewSimpleBindingController : AppKit.NSViewController
    {
        #region Private Variables
        private PersonModel _person = new PersonModel();
        private SqliteConnection Conn;
        #endregion

        #region Computed Properties
        //strongly typed view accessor
        public new SubviewSimpleBinding View {
            get {
                return (SubviewSimpleBinding)base.View;
            }
        }

        [Export("Person")]
        public PersonModel Person {
            get {return _person; }
            set {
                WillChangeValue ("Person");
                _person = value;
                DidChangeValue ("Person");
            }
        }

        public ComboBoxDataSource DataSource {
            get { return EmployeeSelector.DataSource as ComboBoxDataSource; }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public SubviewSimpleBindingController (IntPtr handle) : base (handle)
        {
            Initialize ();
        }

        // Called when created directly from a XIB file
        [Export ("initWithCoder:")]
        public SubviewSimpleBindingController (NSCoder coder) : base (coder)
        {
            Initialize ();
        }

        // Call to load from the XIB/NIB file
        public SubviewSimpleBindingController (SqliteConnection conn) : base ("SubviewSimpleBinding", NSBundle.MainBundle)
        {
            // Initialize
            this.Conn = conn;
            Initialize ();
        }

        // Shared initialization code
        void Initialize ()
        {
        }
        #endregion

        #region Private Methods
        private void LoadSelectedPerson (string id)
        {

            // Found?
            if (id != "") {
                // Yes, load requested record
                Person = new PersonModel (Conn, id);
            }
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Configure Employee selector dropdown
            EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");

            // Wireup events
            EmployeeSelector.Changed += (sender, e) => {
                // Get ID
                var id = DataSource.IDForValue (EmployeeSelector.StringValue);
                LoadSelectedPerson (id);
            };

            EmployeeSelector.SelectionChanged += (sender, e) => {
                // Get ID
                var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
                LoadSelectedPerson (id);
            };

            // Auto select the first person
            EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
            Person = new PersonModel (Conn, DataSource.IDForIndex(0));
    
        }
        #endregion
    }
}
```

Il `DataSource` proprietà fornisce un collegamento di `ComboBoxDataSource` (creato in precedenza) associato alla casella combinata.

Il `LoadSelectedPerson` metodo carica la persona dal database per l'ID univoco specificato:

```csharp
private void LoadSelectedPerson (string id)
{

    // Found?
    if (id != "") {
        // Yes, load requested record
        Person = new PersonModel (Conn, id);
    }
}
```

Nel `AwakeFromNib` override del metodo, prima di tutto si collega un'istanza di origine dati casella combinata personalizzati:

```csharp
EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");
```

Successivamente, Microsoft risponde all'utente modifica il valore di testo della casella combinata cercando l'ID univoco associato (`IDField`) dei dati di presentazione e caricando la persona specificata, se disponibili:

```csharp
EmployeeSelector.Changed += (sender, e) => {
    // Get ID
    var id = DataSource.IDForValue (EmployeeSelector.StringValue);
    LoadSelectedPerson (id);
};
```

È anche possibile caricare una nuova persona se l'utente seleziona un nuovo elemento nell'elenco a discesa:

```csharp
EmployeeSelector.SelectionChanged += (sender, e) => {
    // Get ID
    var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
    LoadSelectedPerson (id);
};
```

Infine, si popolano automaticamente alla casella combinata e persona visualizzato con il primo elemento nell'elenco:

```csharp
// Auto select the first person
EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
Person = new PersonModel (Conn, DataSource.IDForIndex(0));
```

## <a name="sqlitenet-orm"></a>SQLite.NET ORM

Come indicato in precedenza, con open source [SQLite.NET](http://www.sqlite.org) relazione Manager ORM (Object) è possibile ridurre notevolmente la quantità di codice necessario per leggere e scrivere dati da un database SQLite. Ciò potrebbe non essere la route migliore da sfruttare quando si associano dati a causa di alcuni dei requisiti chiave-valore di codifica e il data binding su cui posizionare un oggetto.

In base al sito Web di SQLite.Net _"SQLite è una libreria di software che implementa un motore di database SQL autonomo, senza server, configurazione automatica e transazionale. SQLite è il motore di database distribuito più diffuso al mondo. Il codice sorgente per SQLite è il dominio pubblico"._

Nelle sezioni seguenti, ecco come usare SQLite.Net per fornire dati per una visualizzazione tabella.

### <a name="including-the-sqlitenet-nuget"></a>Tra cui SQLite.net NuGet

SQLite.NET viene presentato come un pacchetto NuGet inclusi nell'applicazione. Prima di poter aggiungere il supporto di database usando SQLite.NET, è necessario includere questo pacchetto.

Eseguire il comando seguente per aggiungere il pacchetto:

1. Nel **riquadro della soluzione**, fare doppio clic il **pacchetti** cartella e selezionare **Aggiungi pacchetti...**
2. Immettere `SQLite.net` nella **casella di ricerca** e selezionare il **sqlite-net** voce:

    [![Aggiunta del pacchetto NuGet di SQLite](databases-images/nuget01.png "aggiunta del pacchetto NuGet di SQLite")](databases-images/nuget01-large.png#lightbox)
3. Scegliere il **Aggiungi pacchetto** per terminare.

### <a name="creating-the-data-model"></a>Creazione del modello di dati

È possibile aggiungere una nuova classe al progetto e chiamare in `OccupationModel`. Successivamente, è possibile modificare il **OccupationModel.cs** file e renderlo simile al seguente:

```csharp
using System;
using SQLite;

namespace MacDatabase
{
    public class OccupationModel
    {
        #region Computed Properties
        [PrimaryKey, AutoIncrement]
        public int ID { get; set; }

        public string Name { get; set;}
        public string Description { get; set;}
        #endregion

        #region Constructors
        public OccupationModel ()
        {
        }

        public OccupationModel (string name, string description)
        {

            // Initialize
            this.Name = name;
            this.Description = description;

        }
        #endregion
    }
}
```

In primo luogo, includiamo SQLite.NET (`using Sqlite`), quindi si espone diverse proprietà, ognuno dei quali verrà scritto nel database quando viene salvato questo record. La prima proprietà è rendere come chiave primaria e impostarlo su incremento automatico come indicato di seguito:

```csharp
[PrimaryKey, AutoIncrement]
public int ID { get; set; }
```
### <a name="initializing-the-database"></a>L'inizializzazione del database

Con le modifiche al nostro modello di dati presenti per supportare la lettura e scrittura al database, è necessario aprire una connessione al database e inizializzarlo alla prima esecuzione. È possibile aggiungere il codice seguente:

```csharp
using SQLite;
...

public SQLiteConnection Conn { get; set; }
...

private SQLiteConnection GetDatabaseConnection() {
    var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
    string db = Path.Combine (documents, "Occupation.db3");
    OccupationModel Occupation;

    // Create the database if it doesn't already exist
    bool exists = File.Exists (db);

    // Create connection to database
    var conn = new SQLiteConnection (db);

    // Initially populate table?
    if (!exists) {
        // Yes, build table
        conn.CreateTable<OccupationModel> ();

        // Add occupations
        Occupation = new OccupationModel ("Documentation Manager", "Manages the Documentation Group");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("Technical Writer", "Writes technical documentation and sample applications");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("Web & Infrastructure", "Creates and maintains the websites that drive documentation");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("API Documentation Manager", "Manages the API Documentation Group");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("API Documenter", "Creates and maintains API documentation");
        conn.Insert (Occupation);
    }

    return conn;
}
```

In primo luogo, si ottiene un percorso del database (Desktop dell'utente in questo caso) e se il database esiste già:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "Occupation.db3");
OccupationModel Occupation;

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
```

Successivamente, viene stabilita la connessione al database nel percorso creato in precedenza:

```csharp
var conn = new SQLiteConnection (db);
```

Infine, si crea la tabella e aggiungere alcuni record predefinito:

```csharp
// Yes, build table
conn.CreateTable<OccupationModel> ();

// Add occupations
Occupation = new OccupationModel ("Documentation Manager", "Manages the Documentation Group");
conn.Insert (Occupation);

Occupation = new OccupationModel ("Technical Writer", "Writes technical documentation and sample applications");
conn.Insert (Occupation);

Occupation = new OccupationModel ("Web & Infrastructure", "Creates and maintains the websites that drive documentation");
conn.Insert (Occupation);

Occupation = new OccupationModel ("API Documentation Manager", "Manages the API Documentation Group");
conn.Insert (Occupation);

Occupation = new OccupationModel ("API Documenter", "Creates and maintains API documentation");
conn.Insert (Occupation);
```

### <a name="adding-a-table-view"></a>Aggiunta di una vista tabella

Come un esempio di utilizzo, si aggiungerà una visualizzazione tabella alla nostra interfaccia utente in Interface builder di Xcode. Si verrà esposto tramite un outlet tabella vista (`OccupationTable`) in modo da potervi accedere tramite il codice c#:

[![Esposizione di un negozio di visualizzazione tabella](databases-images/table01.png "esponendo una presa di visualizzazione tabella")](databases-images/table01-large.png#lightbox)

Successivamente, si aggiungerà le classi personalizzate per popolare la tabella con i dati dal database di SQLite.NET.

### <a name="creating-the-table-data-source"></a>Creazione dell'origine dati tabella

Creare un'origine dati personalizzata per fornire dati per la tabella. In primo luogo, aggiungere una nuova classe denominata `TableORMDatasource` e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;
using SQLite;

namespace MacDatabase
{
    public class TableORMDatasource : NSTableViewDataSource
    {
        #region Computed Properties
        public List<OccupationModel> Occupations { get; set;} = new List<OccupationModel>();
        public SQLiteConnection Conn { get; set; }
        #endregion

        #region Constructors
        public TableORMDatasource (SQLiteConnection conn)
        {
            // Initialize
            this.Conn = conn;
            LoadOccupations ();
        }
        #endregion

        #region Public Methods
        public void LoadOccupations() {

            // Get occupations from database
            var query = Conn.Table<OccupationModel> ();

            // Copy into table collection
            Occupations.Clear ();
            foreach (OccupationModel occupation in query) {
                Occupations.Add (occupation);
            }

        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Occupations.Count;
        }
        #endregion
    }
}
```

Quando si crea un'istanza di questa classe in un secondo momento, si sarà passare la connessione al database SQLite.NET open. Il `LoadOccupations` metodo interroga il database e il record trovati vengono copiati in memoria (tramite la `OccupationModel` modello di dati).

### <a name="creating-the-table-delegate"></a>La creazione del delegato di tabella

La classe finale che occorre è un delegato di tabella personalizzato per visualizzare le informazioni che è stata caricata dal database di SQLite.NET. È possibile aggiungere un nuovo `TableORMDelegate` per il progetto e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;
using SQLite;

namespace MacDatabase
{
    public class TableORMDelegate : NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "OccCell";
        #endregion

        #region Private Variables
        private TableORMDatasource DataSource;
        #endregion

        #region Constructors
        public TableORMDelegate (TableORMDatasource dataSource)
        {
            // Initialize
            this.DataSource = dataSource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Occupation":
                view.StringValue = DataSource.Occupations [(int)row].Name;
                break;
            case "Description":
                view.StringValue = DataSource.Occupations [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Qui usiamo dell'origine dati `Occupations` insieme (che è stato caricato dal database di SQLite.NET) per compilare le colonne della tabella tramite la `GetViewForItem` override del metodo.

### <a name="populating-the-table"></a>Popolamento della tabella

Per tutto è pronto, è possibile popolare la tabella quando si è sottoposto a inflating dal file con estensione xib eseguendo l'override di `AwakeFromNib` metodo e renderla di aspetto analogo al seguente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get database connection
    Conn = GetDatabaseConnection ();

    // Create the Occupation Table Data Source and populate it
    var DataSource = new TableORMDatasource (Conn);

    // Populate the Product Table
    OccupationTable.DataSource = DataSource;
    OccupationTable.Delegate = new TableORMDelegate (DataSource);
}
```

Innanzitutto, abbiamo un guadagno accesso al database SQLite.NET, creando e popolando se non esiste già. Quindi, creiamo una nuova istanza dell'origine dati tabella personalizzato, passare la connessione al database e si collegarlo alla tabella. Infine, si crea una nuova istanza del delegato di tabella personalizzato, passare nell'origine dati e collegarlo alla tabella.

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di data binding e codifica di chiave-valore con i database SQLite in un'applicazione xamarin. Mac. In primo luogo, esaminata l'esposizione di una classe c# Objective-c usando chiave-valore di codifica (i KVM) e chiave-valore osservando (KVO). Successivamente, è stato illustrato come usare una classe conforme KVO e dati associarla agli elementi dell'interfaccia utente in Interface Builder di Xcode. Anche l'articolo ha illustrato utilizzano i dati di SQLite tramite SQLite.NET ORM e visualizzazione dei dati in una visualizzazione tabella.



## <a name="related-links"></a>Collegamenti correlati

- [MacDatabase (esempio)](https://developer.xamarin.com/samples/mac/MacDatabase/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Controlli standard](~/mac/user-interface/standard-controls.md)
- [Visualizzazioni di tabelle](~/mac/user-interface/table-view.md)
- [Visualizzazione della struttura](~/mac/user-interface/outline-view.md)
- [Visualizzazioni di raccolta](~/mac/user-interface/collection-view.md)
- [Guida per programmatori di codifica di tipo chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introduzione alle associazioni Cocoa argomenti di programmazione](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduzione a Cocoa associazioni riferimento](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS Human Interface Guidelines](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
