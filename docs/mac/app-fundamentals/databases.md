---
title: Database in Novell. Mac
description: Questo articolo illustra l'uso della codifica chiave-valore e l'osservazione chiave-valore per consentire data binding tra i database SQLite e gli elementi dell'interfaccia utente nella Interface Builder di Xcode. Viene inoltre illustrata l'utilizzo di SQLite.NET ORM per fornire l'accesso ai dati SQLite.
ms.prod: xamarin
ms.assetid: 44FAFDA8-612A-4E0F-8BB4-5C92A3F4D552
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 893a3f76fe21bfe831ae81ffcd64c1538d9f05e1
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200177"
---
# <a name="databases-in-xamarinmac"></a>Database in Novell. Mac

_Questo articolo illustra l'uso della codifica chiave-valore e l'osservazione chiave-valore per consentire data binding tra i database SQLite e gli elementi dell'interfaccia utente nella Interface Builder di Xcode. Viene inoltre illustrata l'utilizzo di SQLite.NET ORM per fornire l'accesso ai dati SQLite._

## <a name="overview"></a>Panoramica

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere agli stessi database SQLite a cui può accedere un'applicazione Novell. iOS o Novell. Android.

In questo articolo verranno illustrati due modi per accedere ai dati SQLite:

1. **Accesso diretto** : accedendo direttamente a un database SQLite, è possibile usare i dati del database per la codifica chiave-valore e data binding con gli elementi dell'interfaccia utente creati nell'Interface Builder di Xcode. Utilizzando la codifica chiave-valore e le tecniche di data binding nell'applicazione Novell. Mac, è possibile ridurre significativamente la quantità di codice da scrivere e gestire per popolare e utilizzare gli elementi dell'interfaccia utente. Si ha anche il vantaggio di separare ulteriormente i dati di supporto (modello di_dati_) dall'interfaccia utente front-end (_Model-View-Controller_), in modo da semplificare la gestione e la progettazione di applicazioni più flessibili.
2. **SQLite.NET ORM** : usando il [SQLite.NET](http://www.sqlite.org) di gestione delle relazioni degli oggetti Open Source (ORM), è possibile ridurre notevolmente la quantità di codice necessaria per leggere e scrivere dati da un database SQLite. Questi dati possono quindi essere utilizzati per popolare un elemento dell'interfaccia utente, ad esempio una visualizzazione tabella.

[![Esempio di app in esecuzione](databases-images/intro01.png "Esempio di app in esecuzione")](databases-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso della codifica chiave-valore e data binding con i database SQLite in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Poiché si utilizzeranno la codifica e il data binding chiave-valore, usare prima di tutto il [Data Binding e la codifica chiave-valore](~/mac/app-fundamentals/databinding.md) , in quanto verranno analizzate le tecniche e i concetti di base che verranno usati in questa documentazione e nell'applicazione di esempio.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` gli attributi e `Export` usati per collegare le C# classi a Oggetti Objective-C ed elementi dell'interfaccia utente.

## <a name="direct-sqlite-access"></a>Accesso diretto a SQLite

Per i dati SQLite che verranno associati agli elementi dell'interfaccia utente nel Interface Builder di Xcode, è consigliabile accedere direttamente al database SQLite (invece di usare una tecnica come ORM), dal momento che si ha il controllo totale sul modo in cui i dati vengono scritti e letti.  dal database.

Come abbiamo visto nella documentazione relativa al [Data Binding e alla codifica chiave-valore](~/mac/app-fundamentals/databinding.md) , usando la codifica chiave-valore e le tecniche di data binding nell'applicazione Novell. Mac, è possibile ridurre significativamente la quantità di codice da scrivere e mantenere per popolare e usare gli elementi dell'interfaccia utente. In combinazione con l'accesso diretto a un database SQLite, può anche ridurre notevolmente la quantità di codice necessaria per leggere e scrivere dati in tale database.

In questo articolo verrà modificata l'app di esempio dal documento data binding e codice chiave-valore per usare un database SQLite come origine di supporto per l'associazione.

### <a name="including-sqlite-database-support"></a>Inclusione del supporto per i database SQLite

Prima di continuare, è necessario aggiungere il supporto per i database SQLite all'applicazione includendo riferimenti a un paio di. File dll.

Seguire questa procedura:

1. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse sulla cartella **riferimenti** e scegliere **modifica riferimenti**.
2. Selezionare entrambi gli assembly **mono. Data. sqlite** e **System. Data** : 

    [![Aggiunta dei riferimenti necessari](databases-images/reference01.png "Aggiunta dei riferimenti necessari")](databases-images/reference01-large.png#lightbox)
3. Fare clic sul pulsante **OK** per salvare le modifiche e aggiungere i riferimenti.

### <a name="modifying-the-data-model"></a>Modifica del modello di dati

Ora che è stato aggiunto il supporto per l'accesso diretto a un database SQLite all'applicazione, è necessario modificare l'oggetto del modello di dati per leggere e scrivere dati dal database (oltre a fornire codice chiave-valore e data binding). Nel caso dell'applicazione di esempio, si modificherà la classe **PersonModel.cs** e sarà simile alla seguente:

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

Di seguito vengono esaminate le modifiche apportate in dettaglio.

In primo luogo, sono state aggiunte diverse istruzioni using necessarie per usare SQLite ed è stata aggiunta una variabile per salvare l'ultima connessione al database SQLite:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection _conn = null;
```

Questa connessione salvata verrà usata per salvare automaticamente le modifiche apportate al record nel database quando l'utente modifica il contenuto nell'interfaccia utente tramite data binding:

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

Eventuali modifiche apportate alle proprietà **Name**, **occupate** o **Manager** verranno inviate al database se i dati sono stati salvati in precedenza (ad esempio, se la `_conn` variabile non `null`è). Esaminiamo quindi i metodi aggiunti per **creare**, **aggiornare**, **caricare** ed **eliminare** persone dal database.

#### <a name="create-a-new-record"></a>Crea un nuovo record

È stato aggiunto il codice seguente per creare un nuovo record nel database SQLite:

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

Si sta usando un `SQLiteCommand` per creare il nuovo record nel database. Viene ottenuto un nuovo comando da `SQLiteConnection` (conn) passato al metodo chiamando. `CreateCommand` Successivamente, l'istruzione SQL viene impostata in modo da scrivere effettivamente il nuovo record, fornendo i parametri per i valori effettivi:

```csharp
command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";
```

Successivamente `SQLiteCommand`, si impostano i valori per i `Parameters.AddWithValue` parametri usando il metodo su. Usando i parametri, si garantisce che i valori, ad esempio una virgoletta singola, vengano codificati correttamente prima di essere inviati a SQLite. Esempio:

```csharp
command.Parameters.AddWithValue ("@COL1", ID);
```

Infine, poiché una persona può essere un responsabile e avere una raccolta di dipendenti, il `Create` metodo viene chiamato in modo ricorsivo su tali utenti per salvarli anche nel database:

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

È stato aggiunto il codice seguente per aggiornare un record esistente nel database SQLite:

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

Come per la **creazione** precedente, viene `SQLiteCommand` ottenuto un oggetto `SQLiteConnection`dal passato e si imposta SQL per aggiornare il record (fornendo parametri):

```csharp
command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";
```

I valori dei parametri vengono compilati ( `command.Parameters.AddWithValue ("@COL1", ID);`ad esempio,) e di nuovo, viene chiamata in modo ricorsivo Update su tutti i record figlio:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Update sub record
    Person.Update (conn);
}
```

#### <a name="loading-a-record"></a>Caricamento di un record

È stato aggiunto il codice seguente per caricare un record esistente dal database SQLite:

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

Poiché la routine può essere chiamata in modo ricorsivo da un oggetto padre (ad esempio un oggetto Manager che carica il proprio oggetto Employees), è stato aggiunto un codice speciale per gestire l'apertura e la chiusura della connessione al database:

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

Come sempre, abbiamo impostato SQL per recuperare il record e usare i parametri:

```csharp
// Create new command
command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", id);
```

Infine, si usa un lettore di dati per eseguire la query e restituire i campi di record (copiati nell'istanza della `PersonModel` classe):

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

Se questa persona è un responsabile, è necessario caricare anche tutti i dipendenti (anche in questo caso, richiamando il `Load` metodo in modo ricorsivo):

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

Il codice seguente è stato aggiunto per eliminare un record esistente dal database SQLite:

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

Qui viene fornito il SQL per eliminare sia il record Manager che i record di tutti i dipendenti sotto tale responsabile (usando i parametri):

```csharp
// Create new command
command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", ID);
```

Dopo la rimozione del record, viene eliminata l'istanza corrente della `PersonModel` classe:

```csharp
// Empty class
ID = "";
ManagerID = "";
Name = "";
Occupation = "";
isManager = false;
_people = new NSMutableArray();
```

### <a name="initializing-the-database"></a>Inizializzazione del database

Con le modifiche apportate al modello di dati per supportare la lettura e la scrittura nel database, è necessario aprire una connessione al database e inizializzarla alla prima esecuzione. Aggiungere il codice seguente al file **MainWindow.cs** :

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

Esaminiamo ora attentamente il codice precedente. Prima di tutto, viene scelto un percorso per il nuovo database (in questo esempio, il desktop dell'utente), si osservi se il database esiste e, in caso contrario, crearlo:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "People.db3");

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
if (!exists)
    SqliteConnection.CreateFile (db);
```

Viene quindi stabilita la connessione al database usando il percorso creato in precedenza:

```csharp
var conn = new SqliteConnection("Data Source=" + db);
```

Si creeranno quindi tutte le tabelle SQL nel database necessarie:

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

Infine, viene usato il modello di dati`PersonModel`() per creare un set predefinito di record per il database la prima volta che si esegue l'applicazione o se il database è mancante:

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

Quando l'applicazione viene avviata e viene visualizzata la finestra principale, si crea una connessione al database usando il codice aggiunto in precedenza:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get access to database
    DatabaseConnection = GetDatabaseConnection ();
}
```

### <a name="loading-bound-data"></a>Caricamento dei dati associati

Con tutti i componenti per l'accesso diretto ai dati associati da un database SQLite sul posto, è possibile caricare i dati nelle diverse visualizzazioni fornite dall'applicazione e verranno visualizzati automaticamente nell'interfaccia utente.

#### <a name="loading-a-single-record"></a>Caricamento di un singolo record

Per caricare un singolo record in cui l'ID è noto, è possibile usare il codice seguente:

```csharp
Person = new PersonModel (Conn, "0");
```

#### <a name="loading-all-records"></a>Caricamento di tutti i record

Per caricare tutti gli utenti, indipendentemente dal fatto che siano responsabili o meno, usare il codice seguente:

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

Qui viene usato un overload del costruttore per la `PersonModel` classe per caricare la persona in memoria:

```csharp
var person = new PersonModel (_conn, childID);
```

Viene anche chiamata la classe associata ai dati per aggiungere la persona alla raccolta di persone `AddPerson (person)`, in modo da garantire che l'interfaccia utente riconosca la modifica e la visualizzi:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}
```

#### <a name="loading-top-level-records-only"></a>Caricamento solo dei record di primo livello

Per caricare solo i Manager, ad esempio per visualizzare i dati in una visualizzazione struttura, viene usato il codice seguente:

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

L'unica differenza reale nell'istruzione SQL (che carica solo i Manager `command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1"`) ma funziona allo stesso modo della sezione precedente.

<a name="Databases-and-ComboBoxes" />

### <a name="databases-and-comboboxes"></a>Database e ComboBox

I controlli menu disponibili per macOS, ad esempio la casella combinata, possono essere impostati per popolare l'elenco a discesa da un elenco interno, che può essere definito in Interface Builder o popolato tramite codice, oppure fornendo un'origine dati personalizzata esterna. Per altri dettagli, vedere [fornire i dati del controllo menu](~/mac/user-interface/standard-controls.md#Providing-Menu-Control-Data) .

Modificare ad esempio l'esempio di binding semplice precedente in Interface Builder, aggiungere una casella combinata ed esporla usando un Outlet denominato `EmployeeSelector`:

[![Esposizione di un'outlet della casella combinata](databases-images/combo01.png "Esposizione di un'outlet della casella combinata")](databases-images/combo01-large.png#lightbox)

Nel controllo **attributi**selezionare le proprietà **completamento automatico** e **utilizza origini dati** :

![Configurazione degli attributi della casella combinata](databases-images/combo02.png "Configurazione degli attributi della casella combinata")

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

#### <a name="providing-combobox-data"></a>Fornire dati ComboBox

Successivamente, aggiungere una nuova classe al progetto denominata `ComboBoxDataSource` e renderla simile alla seguente:

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

In questo esempio viene creato un nuovo `NSComboBoxDataSource` oggetto che può presentare elementi della casella combinata da qualsiasi origine dati SQLite. In primo luogo, vengono definite le proprietà seguenti:

- `Conn`: Ottiene o imposta una connessione al database SQLite.
- `TableName`: Ottiene o imposta il nome della tabella.
- `IDField`: Ottiene o imposta il campo che fornisce l'ID univoco per la tabella specificata. Il valore predefinito è `ID`.
- `DisplayField`: Ottiene o imposta il campo visualizzato nell'elenco a discesa.
- `RecordCount`: Ottiene il numero di record nella tabella specificata.

Quando si crea una nuova istanza dell'oggetto, vengono passati la connessione, il nome della tabella, facoltativamente il campo ID e il campo di visualizzazione:

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

Viene chiamato ogni volta che viene `TableName`modificato `IDField` il `DisplayField` valore delle proprietà o.

Il `IDForIndex` metodo restituisce l'ID univoco (`IDField`) per il record in corrispondenza dell'indice dell'elemento elenco a discesa specificato: 

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

Il `ValueForIndex` metodo restituisce il valore (`DisplayField`) per l'elemento in corrispondenza dell'indice elenco a discesa specificato:

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

Restituisce il numero pre-calcolato di elementi nell'elenco come calcolato quando vengono modificate le `TableName`proprietà `IDField` , `DisplayField`o: `ItemCount`

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

Si noti che vengono usate le `LIMIT` istruzioni `OFFSET` e nel comando SQLite per limitare il record che è necessario.

Il `IndexOfItem` metodo restituisce l'indice dell'elemento a discesa del`DisplayField`valore () dato:

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

Se il valore non viene trovato, il `NSRange.NotFound` valore viene restituito e tutti gli elementi vengono deselezionati nell'elenco a discesa.

Il `CompletedString` metodo restituisce il primo valore corrispondente (`DisplayField`) per una voce parzialmente tipizzata:

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

Per riunire tutti i componenti, modificare il `SubviewSimpleBindingController` e renderlo simile al seguente:

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

La `DataSource` proprietà fornisce un collegamento `ComboBoxDataSource` a (creato sopra) collegato alla casella combinata.

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

Nell'override `AwakeFromNib` del metodo, prima di tutto si aggiunge un'istanza dell'origine dati della casella combinata personalizzata:

```csharp
EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");
```

Si risponde quindi all'utente che modifica il valore di testo della casella combinata individuando l'ID univoco associato (`IDField`) dei dati che presentano e caricando la persona specificata, se presente:

```csharp
EmployeeSelector.Changed += (sender, e) => {
    // Get ID
    var id = DataSource.IDForValue (EmployeeSelector.StringValue);
    LoadSelectedPerson (id);
};
```

Viene anche caricata una nuova persona se l'utente seleziona un nuovo elemento dall'elenco a discesa:

```csharp
EmployeeSelector.SelectionChanged += (sender, e) => {
    // Get ID
    var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
    LoadSelectedPerson (id);
};
```

Infine, viene popolata automaticamente la casella combinata e la persona visualizzata con il primo elemento nell'elenco:

```csharp
// Auto select the first person
EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
Person = new PersonModel (Conn, DataSource.IDForIndex(0));
```

## <a name="sqlitenet-orm"></a>ORM SQLite.NET

Come indicato in precedenza, usando il [SQLite.NET](http://www.sqlite.org) di gestione delle relazioni degli oggetti Open Source è possibile ridurre notevolmente la quantità di codice necessaria per leggere e scrivere dati da un database SQLite. Questo potrebbe non essere il percorso migliore da eseguire quando si associano i dati a causa di diversi requisiti che codificano e data bindingno i valori chiave per un oggetto.

In base al sito Web di _SQLite.NET, "SQLite è una raccolta software che implementa un motore di database SQL transazionale, senza server, senza server, a configurazione zero. SQLite è il motore di database più ampiamente distribuito in tutto il mondo. Il codice sorgente per SQLite si trova nel dominio public "._

Nelle sezioni seguenti verrà illustrato come utilizzare SQLite.Net per fornire i dati per una visualizzazione tabella.

### <a name="including-the-sqlitenet-nuget"></a>Inclusione di SQLite.net NuGet

SQLite.NET viene presentato come pacchetto NuGet incluso nell'applicazione. Prima di poter aggiungere il supporto del database usando SQLite.NET, è necessario includere questo pacchetto.

Per aggiungere il pacchetto, effettuare le operazioni seguenti:

1. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse sulla cartella **pacchetti** e selezionare **Aggiungi pacchetti...**
2. Immettere `SQLite.net` nella **casella di ricerca** e selezionare la voce **sqlite-net** :

    [![Aggiunta del pacchetto NuGet SQLite](databases-images/nuget01.png "Aggiunta del pacchetto NuGet SQLite")](databases-images/nuget01-large.png#lightbox)
3. Per terminare, fare clic sul pulsante **Aggiungi pacchetto** .

### <a name="creating-the-data-model"></a>Creazione del modello di dati

Si aggiungerà una nuova classe al progetto e si chiamerà `OccupationModel`. Modificare quindi il file **OccupationModel.cs** e apparirà come segue:

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

In primo luogo, è necessario`using Sqlite`includere SQLite.NET (), quindi verranno esposte diverse proprietà, ognuna delle quali verrà scritta nel database quando questo record viene salvato. La prima proprietà viene fatta come chiave primaria e impostata su incremento automatico come segue:

```csharp
[PrimaryKey, AutoIncrement]
public int ID { get; set; }
```

### <a name="initializing-the-database"></a>Inizializzazione del database

Con le modifiche apportate al modello di dati per supportare la lettura e la scrittura nel database, è necessario aprire una connessione al database e inizializzarla alla prima esecuzione. Aggiungere il codice seguente:

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

In primo luogo, si ottiene un percorso del database (il desktop dell'utente in questo caso) e si verifica se il database esiste già:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "Occupation.db3");
OccupationModel Occupation;

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
```

Viene quindi stabilita una connessione al database nel percorso creato in precedenza:

```csharp
var conn = new SQLiteConnection (db);
```

Infine, si crea la tabella e si aggiungono alcuni record predefiniti:

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

### <a name="adding-a-table-view"></a>Aggiunta di una visualizzazione tabella

Come esempio di utilizzo, verrà aggiunta una visualizzazione tabella all'interfaccia utente di Xcode Interface Builder. Questa visualizzazione tabella verrà esposta tramite un Outlet (`OccupationTable`) in modo che sia possibile accedervi tramite C# codice:

[![Esposizione di un outlet di visualizzazione tabella](databases-images/table01.png "Esposizione di un outlet di visualizzazione tabella")](databases-images/table01-large.png#lightbox)

Successivamente, verranno aggiunte le classi personalizzate per popolare la tabella con i dati del database SQLite.NET.

### <a name="creating-the-table-data-source"></a>Creazione dell'origine dati della tabella

Viene ora creata un'origine dati personalizzata per fornire i dati per la tabella. In primo luogo, aggiungere una nuova `TableORMDatasource` classe denominata e renderla simile alla seguente:

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

Quando si crea un'istanza di questa classe in un secondo momento, si passerà la connessione al database open SQLite.NET. Il `LoadOccupations` metodo esegue una query sul database e copia i record trovati in memoria ( `OccupationModel` usando il modello di dati).

### <a name="creating-the-table-delegate"></a>Creazione del delegato della tabella

La classe finale necessaria è un delegato di tabella personalizzato per visualizzare le informazioni caricate dal database SQLite.NET. Aggiungere un nuovo `TableORMDelegate` al progetto e renderlo simile al seguente:

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

Qui viene `Occupations` usata la raccolta dell'origine dati (che è stata caricata dal database SQLite.NET) per compilare le colonne della tabella tramite l' `GetViewForItem` override del metodo.

### <a name="populating-the-table"></a>Popolamento della tabella

Con tutti i componenti disponibili, è necessario popolare la tabella quando viene ingrandita dal file con estensione XIB eseguendo l'override del `AwakeFromNib` metodo e rendendola simile alla seguente:

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

Prima di tutto, si ottiene l'accesso al database SQLite.NET, che viene creato e popolato se non esiste già. Si crea quindi una nuova istanza dell'origine dati della tabella personalizzata, si passa la connessione al database e la si collega alla tabella. Infine, si crea una nuova istanza del delegato della tabella personalizzata, si passa l'origine dati e la si collega alla tabella.

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso di data binding e la codifica chiave-valore con i database SQLite in un'applicazione Novell. Mac. In primo luogo, è stata esaminata C# l'esposizione di una classe a Objective-C utilizzando la codifica chiave-valore (KVC) e l'osservazione chiave-valore (KVO). Successivamente, è stato illustrato come usare una classe conforme a KVO e i dati lo associano agli elementi dell'interfaccia utente nel Interface Builder di Xcode. Questo articolo illustra anche l'uso di dati SQLite tramite SQLite.NET ORM e la visualizzazione di tali dati in una vista tabella.



## <a name="related-links"></a>Collegamenti correlati

- [MacDatabase (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabase)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data Binding e codifica chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Controlli standard](~/mac/user-interface/standard-controls.md)
- [Viste tabella](~/mac/user-interface/table-view.md)
- [Visualizzazione struttura](~/mac/user-interface/outline-view.md)
- [Viste di raccolta](~/mac/user-interface/collection-view.md)
- [Guida alla programmazione del codice chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introduzione agli argomenti di programmazione delle associazioni Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Informazioni di riferimento sulle associazioni di Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
