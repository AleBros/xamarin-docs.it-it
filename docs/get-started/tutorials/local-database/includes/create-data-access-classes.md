---
ms.openlocfilehash: b11a5972c2aabace8a6991a82f5719f34450297d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841545"
---
In questo esercizio verranno aggiunte classi di accesso ai dati al progetto **LocalDatabaseTutorial**, che verrà usato per rendere persistenti i dati relativi alle persone nel database.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **Esplora soluzioni** aggiungere una nuova classe denominata `Person` al progetto **LocalDatabaseTutorial**. In **Person.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Questo codice definisce una classe `Person` che archivierà i dati relativi a ogni persona nell'applicazione. La proprietà `ID` è contrassegnata con gli attributi `PrimaryKey` e `AutoIncrement` per garantire che ogni istanza di `Person` nel database abbia un ID univoco fornito da SQLite.NET.

1. In **Esplora soluzioni** aggiungere una nuova classe denominata `Database` al progetto **LocalDatabaseTutorial**. In **Database.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Questa classe contiene il codice per creare il database, leggere i dati dal database e scrivere i dati in esso. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `Database` accetta il percorso del file di database come argomento. Questo percorso verrà fornito dalla classe `App` nell'esercizio successivo.

1. In **Esplora soluzioni** espandere **App.xaml** nel progetto **LocalDatabaseTutorial** e fare doppio clic su **App.xaml.cs** per aprirlo. In **App.xaml.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Questo codice definisce una proprietà `Database` che crea una nuova istanza di `Database` come singleton. Un percorso di file locale e un nome di file, che rappresentano la posizione in cui archiviare il database, vengono passati come argomento per il costruttore della classe `Database`.

    > [!IMPORTANT]
    > Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **riquadro della soluzione** aggiungere una nuova classe denominata `Person` al progetto **LocalDatabaseTutorial**. In **Person.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Questo codice definisce una classe `Person` che archivierà i dati relativi a ogni persona nell'applicazione. La proprietà `ID` è contrassegnata con gli attributi `PrimaryKey` e `AutoIncrement` per garantire che ogni istanza di `Person` nel database abbia un ID univoco fornito da SQLite.NET.

1. Nel **riquadro della soluzione** aggiungere una nuova classe denominata `Database` al progetto **LocalDatabaseTutorial**. In **Database.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Questa classe contiene il codice per creare il database, leggere i dati dal database e scrivere i dati in esso. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `Database` accetta il percorso del file di database come argomento. Questo percorso verrà fornito dalla classe `App` nell'esercizio successivo.

1. Nel **riquadro della soluzione** espandere **App.xaml** nel progetto **LocalDatabaseTutorial** e fare doppio clic su **App.xaml.cs** per aprirlo. In **App.xaml.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Questo codice definisce una proprietà `Database` che crea una nuova istanza di `Database` come singleton. Un percorso di file locale e un nome di file, che rappresentano la posizione in cui archiviare il database, vengono passati come argomento per il costruttore della classe `Database`.

    > [!IMPORTANT]
    > Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.
