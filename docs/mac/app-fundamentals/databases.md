---
title: Database in Xamarin.Mac
description: In questo articolo viene illustrato l'utilizzo chiave-valore di codifica e l'analisi per consentire l'associazione dati tra database SQLite e gli elementi dell'interfaccia utente in interfaccia generatore del Xcode chiave-valore. Descrive inoltre utilizzando il ORM SQLite.NET per fornire l'accesso ai dati di SQLite.
ms.prod: xamarin
ms.assetid: 44FAFDA8-612A-4E0F-8BB4-5C92A3F4D552
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3bc40824396aba78b2645bf9701e8e0e659c8b0a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791992"
---
# <a name="databases-in-xamarinmac"></a>Database in Xamarin.Mac

_In questo articolo viene illustrato l'utilizzo chiave-valore di codifica e l'analisi per consentire l'associazione dati tra database SQLite e gli elementi dell'interfaccia utente in interfaccia generatore del Xcode chiave-valore. Descrive inoltre utilizzando il ORM SQLite.NET per fornire l'accesso ai dati di SQLite._

## <a name="overview"></a>Panoramica

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere ai database SQLite stesso che possa accedere a un'applicazione di xamarin. IOS o xamarin.

In questo articolo è verranno illustrati due modi per accedere ai dati di SQLite:

1. **L'accesso diretto a** : accedendo direttamente un SQLite Database, è possibile utilizzare i dati dal database per la codifica chiave-valore e l'associazione dati con elementi dell'interfaccia utente creati in Generatore di interfaccia di Xcode. Tramite chiave-valore di codifica di data binding e le tecniche Xamarin.Mac nell'applicazione in uso, è possibile ridurre notevolmente la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È inoltre il vantaggio di separazione ulteriormente i dati di backup (_modello di dati_) la parte anteriore terminare l'interfaccia utente (_Model-View-Controller_), i punti iniziali per più semplici da gestire, più flessibile dell'applicazione progettazione.
2. **ORM SQLite.NET** - open source [SQLite.NET](http://www.sqlite.org) relazione Manager ORM (Object) è in grado di ridurre notevolmente la quantità di codice necessario per leggere e scrivere i dati da un database SQLite. Questo dati possono quindi essere utilizzati per popolare un elemento dell'interfaccia utente, ad esempio una visualizzazione tabella.

[![Un esempio di app in esecuzione](databases-images/intro01.png "un esempio di app in esecuzione")](databases-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo chiave-valore di codifica e l'associazione di dati con i database SQLite in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Poiché si utilizzerà la codifica chiave-valore e l'associazione dati, utilizzare tramite il [Data binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) in primo luogo, come le tecniche di base e verranno trattati i concetti che verranno usati in questa documentazione e il relativo esempio applicazione.

È possibile dare un'occhiata il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` gli attributi utilizzato per associare le classi c# Objective-C e agli oggetti dell'interfaccia utente gli elementi.

## <a name="direct-sqlite-access"></a>SQLite accesso diretto

Per i dati di SQLite che sta per essere associato a elementi dell'interfaccia utente in Generatore del Xcode di interfaccia, è altamente consigliabile che si accede al database SQLite direttamente (anziché tramite una tecnica, ad esempio una ORM), poiché si dispone di un controllo totale sulla modalità dei dati vengono scritti e letti  dal database.

Come abbiamo visto nella [Data Binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione, con chiave-valore di codifica di data binding e le tecniche Xamarin.Mac nell'applicazione in uso, è possibile ridurre la quantità di codice che è necessario scrivere e Gestisci per popolare e lavorare con gli elementi dell'interfaccia utente. In combinazione con accesso diretto a un database SQLite, considerevole ridurre la quantità di codice necessario per leggere e scrivere dati in tale database.

In questo articolo, verrà modificata l'app di esempio dall'associazione di dati del documento codifica chiave-valore per utilizzare un SQLite Database come origine di backup per l'associazione.

### <a name="including-sqlite-database-support"></a>Incluso il supporto di database SQLite

Prima di continuare, è necessario aggiungere il supporto per database SQLite per l'applicazione con l'inclusione di riferimenti a un paio di. File DLL.

Seguire questa procedura:

1. Nel **soluzione riempimento**, fare clic su di **riferimenti** cartella e selezionare **Modifica riferimenti**.
2. Selezionare sia la **Mono.Data.Sqlite** e **System. Data** assembly: 

    [![Aggiungere i riferimenti necessari](databases-images/reference01.png "aggiungendo i riferimenti necessari")](databases-images/reference01-large.png#lightbox)
3. Fare clic su di **OK** pulsante per salvare le modifiche e aggiungere i riferimenti.

### <a name="modifying-the-data-model"></a>Modificare il modello di dati

Ora che è stato aggiunto supporto per accedere direttamente a un database SQLite per l'applicazione, è necessario modificare l'oggetto modello di dati per leggere e scrivere i dati dal database (nonché fornire chiave-valore di codifica e l'associazione di dati). Nel caso di applicazione di esempio, si modificherà il **PersonModel.cs** classe e renderlo simile al seguente:

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

Esaminiamo un le modifiche in dettaglio di seguito.

In primo luogo, sono state aggiunte diverse utilizzando le istruzioni che è necessarie utilizzare SQLite e sono stati aggiunti a una variabile in cui salvare l'ultima connessione al database SQLite:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection _conn = null;
```

Questa connessione salvata verrà utilizzata per salvare automaticamente eventuali modifiche apportate al record di database quando l'utente modifica il contenuto nell'interfaccia utente tramite l'associazione dati:

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

Qualsiasi modifica apportata al **nome**, **occupazione** o **isManager** proprietà verranno inviate al database se i dati non sono stati salvati non esiste (ad esempio, se il `_conn` la variabile non è `null`). Successivamente, si esaminerà i metodi che è stato aggiunto al **crea**, **aggiornamento**, **carico** e **eliminare** utenti dal database.

#### <a name="create-a-new-record"></a>Creare un nuovo record

Per creare un nuovo record nel database SQLite è stato aggiunto il seguente codice:

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

Si sta usando un `SQLiteCommand` per creare il nuovo record nel database. Si ottiene un nuovo comando di `SQLiteConnection` (conn) che viene passato al metodo chiamando `CreateCommand`. Successivamente, viene impostato l'istruzione SQL per scrivere il nuovo record, specificando i parametri per i valori effettivi:

```csharp
command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";
```

In un secondo momento si impostare i valori per i parametri usando il `Parameters.AddWithValue` metodo il `SQLiteCommand`. Utilizzando i parametri, è assicurarsi che i valori (ad esempio una virgoletta singola) vengono codificati correttamente prima di inviarlo al SQLite. Esempio:

```csharp
command.Parameters.AddWithValue ("@COL1", ID);
```

Infine, poiché una persona può essere un responsabile e dispone di una raccolta di dipendenti in essi, siamo in modo ricorsivo la chiamata di `Create` metodo persone per salvarle nel database:

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

Per aggiornare un record esistente nel database SQLite è stato aggiunto il seguente codice:

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

Ad esempio **crea** sopra, si ottiene un `SQLiteCommand` dall'oggetto passato in `SQLiteConnection`e impostare il nostro SQL per aggiornare i record (specificando i parametri):

```csharp
command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";
```

È inserire i valori di parametro (esempio: `command.Parameters.AddWithValue ("@COL1", ID);`) e, in modo ricorsivo chiamare di nuovo update su qualsiasi elemento figlio record:

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

Per caricare un record esistente dal database SQLite è stato aggiunto il seguente codice:

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

La routine può essere chiamata da un oggetto padre (ad esempio un oggetto di gestione durante il caricamento dell'oggetto di dipendenti) in modo ricorsivo, era stato aggiunto codice speciale per la gestione di apertura e chiusura della connessione al database:

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

Come sempre, viene impostato il nostro SQL per recuperare il record e utilizzare i parametri:

```csharp
// Create new command
command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", id);
```

Infine, si utilizza un lettore di dati per eseguire la query e restituire i campi di record (che viene copiato nell'istanza di `PersonModel` classe):

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

Se l'utente è una gestione, è necessario caricare anche tutti i dipendenti (nuovamente, chiamando il metodo in modo ricorsivo i `Load` (metodo)):

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

Per eliminare un record esistente dal database SQLite è stato aggiunto il seguente codice:

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

Di seguito si fornisce l'istruzione SQL per eliminare il record di gestori e i record di tutti i dipendenti con tale manager (utilizzo di parametri):

```csharp
// Create new command
command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", ID);
```

Dopo il record è stato rimosso, l'istanza corrente di è cancellare la `PersonModel` classe:

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

Con le modifiche per il modello di dati sul posto per supportare la lettura e scrittura al database, è necessario aprire una connessione al database e inizializzarlo alla prima esecuzione. Aggiungere il seguente codice al nostro **MainWindow.cs** file:

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

Diamo un'occhiata il codice precedente. In primo luogo, si selezionare un percorso per il nuovo database (in questo esempio, il Desktop dell'utente), verificare se il database esiste e in caso contrario, crearla:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "People.db3");

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
if (!exists)
    SqliteConnection.CreateFile (db);
```

Successivamente, si stabilisce la connessione al database utilizzando il percorso creato in precedenza:

```csharp
var conn = new SqliteConnection("Data Source=" + db);
```

È quindi creare tutte le tabelle SQL nel database che è necessario:

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

Infine, è utilizzare il modello di dati (`PersonModel`) per creare un set predefinito di record per l'ora del database prima dell'esecuzione dell'applicazione o se il database è mancano:

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

Quando l'applicazione viene avviata e verrà visualizzata la finestra principale, è stabilire una connessione al database utilizzando il codice che è stato aggiunto in precedenza:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get access to database
    DatabaseConnection = GetDatabaseConnection ();
}
```

### <a name="loading-bound-data"></a>Il caricamento di dati associati

Con tutti i componenti per accedere direttamente a dati associati da un database SQLite sul posto, è possibile caricare i dati in diverse visualizzazioni che fornisce l'applicazione e verrà automaticamente visualizzato nell'interfaccia utente.

#### <a name="loading-a-single-record"></a>Il caricamento di un singolo record

Per caricare un singolo record in cui l'ID è noto, è possibile utilizzare il codice seguente:

```csharp
Person = new PersonModel (Conn, "0");
```

#### <a name="loading-all-records"></a>Il caricamento di tutti i record

Per caricare tutti gli utenti, indipendentemente dal fatto che se si tratta di un manager o non, utilizzare il codice seguente:

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

In questo caso, viene usato un overload del costruttore per la `PersonModel` classe per l'utente viene caricato in memoria:

```csharp
var person = new PersonModel (_conn, childID);
```

Qui viene chiamato anche la classe con associazione a dati per aggiungere l'utente per l'insieme delle persone `AddPerson (person)`, in questo modo che riconosca la modifica e lo visualizza l'interfaccia utente:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}
```

#### <a name="loading-top-level-records-only"></a>Caricare solo i record di livello superiore

Per caricare solo le Gestioni (ad esempio, per visualizzare i dati in una visualizzazione struttura), è il codice seguente:

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

La differenza reale solo nell'istruzione SQL in (che carica solo i responsabili `command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1"`), ma la funziona come la sezione precedente in caso contrario.

<a name="Databases-and-ComboBoxes" />

### <a name="databases-and-comboboxes"></a>I database e caselle combinate

I controlli Menu disponibili per macOS (ad esempio, la casella combinata) può essere impostati su popolare l'elenco a discesa da un elenco interno (che può essere predefinito in Generatore di interfaccia o popolato tramite codice) o fornendo una propria origine dati esterna e personalizzati. Vedere [che fornisce dati di controllo Menu](~/mac/user-interface/standard-controls.md#Providing-Menu-Control-Data) per altri dettagli.

Ad esempio, modificare l'esempio sopra Binding semplice in Generatore di interfaccia, aggiungere una casella combinata ed esporlo mediante una presa denominata `EmployeeSelector`:

[![Esposizione di un Negozio o casella combinata](databases-images/combo01.png "esposizione di un Negozio o casella combinata")](databases-images/combo01-large.png#lightbox)

Nel **attributi controllo**, controllare il **Autocompletes** e **Usa origine dati** proprietà:

![Configurare gli attributi della casella combinata](databases-images/combo02.png "configurazione gli attributi della casella combinata")

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

In questo esempio viene creato un nuovo `NSComboBoxDataSource` che può presentare gli elementi di casella combinata da qualsiasi origine dati di SQLite. In primo luogo, è possibile definire le proprietà seguenti:

- `Conn` : Ottiene o imposta una connessione al database SQLite.
- `TableName` : Ottiene o imposta il nome della tabella.
- `IDField` : Ottiene o imposta il campo che fornisce l'ID univoco per la tabella specificata. Il valore predefinito è `ID`.
- `DisplayField` : Ottiene o imposta il campo che viene visualizzato nell'elenco a discesa.
- `RecordCount` -Ottiene il numero di record nella tabella specificata.

Quando si crea una nuova istanza dell'oggetto, vengono passati nella connessione, nome della tabella, facoltativamente, il campo ID e il campo di visualizzazione:

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

Viene chiamato ogni volta che il `TableName`, `IDField` o `DisplayField` viene modificato il valore di proprietà.

Il `IDForIndex` il metodo restituisce l'ID univoco (`IDField`) per il record in corrispondenza dell'indice di elemento di elenco a discesa specificato: 

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

Il `ValueForIndex` metodo restituisce il valore (`DisplayField`) per l'elemento in corrispondenza dell'indice di elenco a discesa specificato:

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

Il `IDForValue` il metodo restituisce l'ID univoco (`IDField`) per il valore specificato (`DisplayField`):

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

Il `ItemCount` restituisce il numero di pre-calcolato di elementi nell'elenco come calcolato quando il `TableName`, `IDField` o `DisplayField` si modificano le proprietà:

```csharp
public override nint ItemCount (NSComboBox comboBox)
{
    return RecordCount;
}
```

Il `ObjectValueForItem` metodo fornisce il valore (`DisplayField`) per l'indice dell'elemento di elenco a discesa specificato:

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

Si noti che si sono usando il `LIMIT` e `OFFSET` istruzioni in questo comando SQLite per limitare al un record ci stiamo necessarie.

Il `IndexOfItem` metodo restituisce l'indice dell'elemento di elenco a discesa del valore (`DisplayField`) specificato:

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

Se il valore non viene trovato, il `NSRange.NotFound` valore restituito e vengono deselezionati tutti gli elementi nell'elenco a discesa.

Il `CompletedString` il metodo restituisce il primo valore corrispondente (`DisplayField`) per una voce parzialmente tipizzata:

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

Per visualizzare tutte le parti tra loro, modificare il `SubviewSimpleBindingController` e renderlo simile al seguente:

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

Il `LoadSelectedPerson` metodo carica l'utente dal database per l'ID univoco specificato:

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

Nel `AwakeFromNib` override del metodo, innanzitutto si collega un'istanza di origine dati casella combinata personalizzato:

```csharp
EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");
```

Successivamente, risposta per l'utente modifica il valore di testo della casella combinata cercando l'ID univoco associato (`IDField`) dei dati di presentazione e il caricamento di persona specificata se trovato:

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

Infine, si popolano automaticamente alla casella combinata e una persona visualizzato con il primo elemento nell'elenco:

```csharp
// Auto select the first person
EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
Person = new PersonModel (Conn, DataSource.IDForIndex(0));
```

## <a name="sqlitenet-orm"></a>SQLite.NET ORM

Come descritto in precedenza, tramite open source [SQLite.NET](http://www.sqlite.org) relazione Manager ORM (Object) è in grado di ridurre notevolmente la quantità di codice necessario per leggere e scrivere i dati da un database SQLite. Questo potrebbe non essere la route migliore da intraprendere quando l'associazione di dati a causa di alcuni dei requisiti di chiave-valore di codifica e l'associazione dati su cui posizionare un oggetto.

In base al sito Web di SQLite.Net _"SQLite è una libreria di software che implementa un motore di database SQL autonomo, senza server, senza operazioni di configurazione e transazionale. SQLite è il motore di database più ampiamente distribuito nel mondo. Il codice sorgente per SQLite è il dominio pubblico."_

Nelle sezioni seguenti, vi mostreremo come utilizzare SQLite.Net per fornire dati per una visualizzazione tabella.

### <a name="including-the-sqlitenet-nuget"></a>Tra cui SQLite.net NuGet

SQLite.NET viene presentato come un pacchetto NuGet che incluse nell'applicazione. Prima di aggiungere il supporto di database con SQLite.NET, è necessario includere questo pacchetto.

Eseguire il comando seguente per aggiungere il pacchetto:

1. Nel **soluzione riempimento**, fare doppio clic su di **pacchetti** cartella e selezionare **Aggiungi pacchetti...**
2. Immettere `SQLite.net` nel **casella di ricerca** e selezionare il **sqlite net** voce:

    [![Aggiunta del pacchetto SQLite NuGet](databases-images/nuget01.png "aggiunta del pacchetto SQLite NuGet")](databases-images/nuget01-large.png#lightbox)
3. Fare clic su di **Aggiungi pacchetto** pulsante Fine.

### <a name="creating-the-data-model"></a>Creazione del modello di dati

È ora possibile aggiungere una nuova classe al progetto e chiamare in `OccupationModel`. Successivamente, si modifica il **OccupationModel.cs** file e renderlo simile al seguente:

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

In primo luogo, è stato incluso SQLite.NET (`using Sqlite`), quindi è necessario esporre diverse proprietà, ognuno dei quali verrà scritto nel database quando questo record viene salvato. La prima proprietà è rendere come chiave primaria e impostarla su incremento automatico, come indicato di seguito:

```csharp
[PrimaryKey, AutoIncrement]
public int ID { get; set; }
```
### <a name="initializing-the-database"></a>Inizializzazione del database

Con le modifiche per il modello di dati sul posto per supportare la lettura e scrittura al database, è necessario aprire una connessione al database e inizializzarlo alla prima esecuzione. Aggiungere il codice seguente:

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

È innanzitutto ottenere un percorso del database (Desktop dell'utente in questo caso) e se il database esiste già:

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

### <a name="adding-a-table-view"></a>Aggiunta di una visualizzazione tabella

Come un esempio di utilizzo, verrà aggiunto una visualizzazione tabella per l'interfaccia utente in Generatore di interfaccia di Xcode. Si espone questa visualizzazione tabella tramite una presa di corrente (`OccupationTable`) in modo che è possibile accedervi tramite codice c#:

[![Esposizione di un punto di vista tabella](databases-images/table01.png "esposizione di un Negozio o visualizzazione tabella")](databases-images/table01-large.png#lightbox)

Successivamente, aggiungeremo le classi personalizzate per popolare la tabella con i dati dal database SQLite.NET.

### <a name="creating-the-table-data-source"></a>Creazione dell'origine dati tabella

Creare un'origine dati personalizzata per fornire dati per la tabella. Aggiungere innanzitutto una nuova classe denominata `TableORMDatasource` e renderlo simile al seguente:

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

Quando si crea un'istanza di questa classe in un secondo momento, si sarà passare la connessione di database SQLite.NET aperta. Il `LoadOccupations` metodo interroga il database e copia i record presenti in memoria (tramite il nostro `OccupationModel` modello di dati).

### <a name="creating-the-table-delegate"></a>Creazione del delegato di tabella

La classe finale, che è necessario è un delegato personalizzato di tabella per visualizzare le informazioni che è stato caricato dal database SQLite.NET. Aggiungere un nuovo `TableORMDelegate` al progetto e renderlo simile al seguente:

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

Di seguito viene usato l'origine dati `Occupations` insieme (che è stati caricati dal database SQLite.NET) per riempire le colonne della tabella tramite il `GetViewForItem` override del metodo.

### <a name="populating-the-table"></a>Popolamento della tabella

Con tutti i componenti sul posto, si popolare la tabella quando è state alterata dal file .xib eseguendo l'override di `AwakeFromNib` metodo e si rende l'aspetto è simile alla seguente:

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

In primo luogo, si accedere al database SQLite.NET, creazione e la compilazione se non esiste già. Verrà quindi creata una nuova istanza dell'origine dati tabella personalizzato, passare la connessione al database e si collegarlo alla tabella. Infine, si crea una nuova istanza del delegato di tabella personalizzata, passare l'origine dati e collegarlo alla tabella.

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di data binding e la generazione di codice chiave-valore con i database SQLite in un'applicazione Xamarin.Mac un'analisi approfondita. Prima di tutto, esaminato l'esposizione di una classe c# per Objective-C utilizzando chiave-valore di codifica (i KVM) e chiave-valore osservando (KVO). Successivamente, è stato spiegato come utilizzare una classe conforme KVO e dati associarlo a elementi dell'interfaccia utente in Generatore del Xcode di interfaccia. L'articolo vengono descritte anche utilizzano i dati di SQLite tramite il ORM SQLite.NET e visualizzazione dei dati in una visualizzazione tabella.



## <a name="related-links"></a>Collegamenti correlati

- [MacDatabase (esempio)](https://developer.xamarin.com/samples/mac/MacDatabase/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Controlli standard](~/mac/user-interface/standard-controls.md)
- [Viste delle tabelle](~/mac/user-interface/table-view.md)
- [Visualizzazioni della struttura](~/mac/user-interface/outline-view.md)
- [Viste di raccolta](~/mac/user-interface/collection-view.md)
- [Guida per programmatori di codifica chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introduzione alle associazioni Cocoa argomenti di programmazione](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduzione a Cocoa associazioni riferimento](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
