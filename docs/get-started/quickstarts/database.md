---
title: Archiviare i dati in un database locale di SQLite.NET
description: Questo articolo illustra come archiviare i dati in un database SQLite.NET locale.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: ff07af00e1e647255ac56318c0685552823f510c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653513"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Archiviare i dati in un database SQLite.NET locale

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

In questa Guida introduttiva si apprenderà come:

- Usare Gestione pacchetti NuGet per aggiungere un pacchetto NuGet a un progetto.
- Archiviare i dati in locale in un database SQLite.NET.

La Guida introduttiva illustra come archiviare i dati in un database SQLite.NET locale. Il risultato è riportato di seguito:

[ ![(database-images/screenshots1-sml.png " ")]Pagina note] (database-images/screenshots1.png#lightbox "Pagina note") Nota pagina immissione(database-images/screenshots2.png#lightbox "Nota pagina voce") [ ![(database-images/screenshots2-sml.png " ")]] 


### <a name="prerequisites"></a>Prerequisiti

Prima di provare questa Guida introduttiva, è necessario completare correttamente la [Guida introduttiva precedente](multi-page.md) . In alternativa, scaricare l' [esempio di Guida introduttiva precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio e aprire la soluzione note.

2. In **Esplora soluzioni**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Gestisci pacchetti NuGet...** :

    ![](database-images/vs/add-nuget-packages.png "Aggiunta di pacchetti NuGet")    

3. In **Gestione pacchetti NuGet** selezionare la scheda **Sfoglia**, cercare il pacchetto NuGet **sqlite-net-pcl**, selezionarlo e fare clic sul pulsante **Installa** per aggiungerlo al progetto:

    ![](database-images/vs/add-package.png "Aggiungi pacchetto")

    > [!NOTE]
    > Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:
    > - **Autore/i:** Frank A. Krueger
    > - **ID:** sqlite-net-pcl
    > - **Collegamento NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Nonostante il nome del pacchetto, questo pacchetto NuGet può essere usato anche nei progetti .NET Standard.

    Il pacchetto verrà usato per integrare le operazioni di database nell'applicazione.

4. In **Esplora soluzioni**, nel progetto **note** , aprire **note.cs** nella cartella **Models** e sostituire il codice esistente con il codice seguente:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Questa classe definisce un `Note` modello in cui vengono archiviati i dati relativi a ogni nota nell'applicazione. La `ID` proprietà è contrassegnata `PrimaryKey` con `AutoIncrement` gli attributi e per garantire `Note` che ogni istanza nel database SQLite.NET disponga di un ID univoco fornito da SQLite.NET.

    Salvare le modifiche apportate a **note.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

5. In **Esplora soluzioni**aggiungere una nuova cartella denominata **Data** al progetto **Note** .

6. In **Esplora soluzioni**, nel progetto **note** , aggiungere una nuova classe denominata **NoteDatabase** alla cartella **dati** .

7. In **NoteDatabase.cs**sostituire il codice esistente con il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Questa classe contiene codice per la creazione del database, la lettura dei dati, la scrittura dei dati e l'eliminazione dei dati. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `NoteDatabase` accetta il percorso del file di database come argomento. Questo percorso verrà fornito dalla `App` classe nel passaggio successivo.

    Salvare le modifiche apportate a **NoteDatabase.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

8. In **Esplora soluzioni**, nel progetto **note** , fare doppio clic su **app.XAML.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
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

    Questo codice definisce una `Database` proprietà che crea una nuova `NoteDatabase` istanza come singleton, passando il nome file del database `NoteDatabase` come argomento al costruttore. Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

9. In **Esplora soluzioni**, nel progetto **note** , fare doppio clic su **NotesPage.XAML.cs** per aprirlo. Sostituire quindi il `OnAppearing` metodo con il codice seguente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note archiviate nel database.

    Salvare le modifiche apportate a **NotesPage.XAML.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

10. In **Esplora soluzioni**fare doppio clic su **NoteEntryPage.XAML.cs** per aprirlo. Sostituire quindi i `OnSaveButtonClicked` metodi `OnDeleteButtonClicked` e con il codice seguente:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      Archivia un' `Note` istanza di che rappresenta una singola nota nell'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. `NoteEntryPage` Quando viene `OnSaveButtonClicked` eseguito il gestore eventi, l' `Note` istanza viene salvata nel database e l'applicazione torna alla pagina precedente. Quando viene `OnDeleteButtonClicked` eseguito il gestore eventi, l' `Note` istanza viene eliminata dal database e l'applicazione torna alla pagina precedente.

      Salvare le modifiche apportate a **NoteEntryPage.XAML.cs** premendo **CTRL + S**e chiudere il file.

11. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il **+** pulsante per passare al **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere un numero di note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac e aprire il progetto note.

2. Nella **riquadro della soluzione**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > Aggiungi pacchetti NuGet...** :

    ![](database-images/vsmac/add-nuget-packages.png "Aggiunta di pacchetti NuGet")    

3. Nella finestra **Aggiungi pacchetti** cercare il pacchetto NuGet **sqlite-net-pcl**, selezionarlo e fare clic sul pulsante **Aggiungi pacchetto** per aggiungerlo al progetto:

    ![](database-images/vsmac/add-package.png "Aggiungi pacchetto")

    > [!NOTE]
    > Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:
    > - **Autore:** Frank A. Krueger
    > - **ID:** sqlite-net-pcl
    > - **Collegamento NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Nonostante il nome del pacchetto, questo pacchetto NuGet può essere usato anche nei progetti .NET Standard.

    Il pacchetto verrà usato per integrare le operazioni di database nell'applicazione.

4. Nel **riquadro della soluzione**, nel progetto **note** , aprire **note.cs** nella cartella Models e sostituire il codice esistente con il codice seguente:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Questa classe definisce un `Note` modello in cui vengono archiviati i dati relativi a ogni nota nell'applicazione. La `ID` proprietà è contrassegnata `PrimaryKey` con `AutoIncrement` gli attributi e per garantire `Note` che ogni istanza nel database SQLite.NET disponga di un ID univoco fornito da SQLite.NET.

    Salvare le modifiche apportate a **note.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

5. Nella **riquadro della soluzione**aggiungere una nuova cartella denominata **Data** al progetto **Note** .

6. Nel **riquadro della soluzione**nel progetto **note** aggiungere una nuova classe denominata **NoteDatabase** alla cartella **dati** .

7. In **NoteDatabase.cs**sostituire il codice esistente con il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Questa classe contiene codice per la creazione del database, la lettura dei dati, la scrittura dei dati e l'eliminazione dei dati. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `NoteDatabase` accetta il percorso del file di database come argomento. Questo percorso verrà fornito dalla `App` classe nel passaggio successivo.

    Salvare le modifiche apportate a **NoteDatabase.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

8. Nel **riquadro della soluzione**, nel progetto **note** , fare doppio clic su **app.XAML.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
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

    Questo codice definisce una `Database` proprietà che crea una nuova `NoteDatabase` istanza come singleton, passando il nome file del database `NoteDatabase` come argomento al costruttore. Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

9. Nel **riquadro della soluzione**, nel progetto **note** , fare doppio clic su **NotesPage.XAML.cs** per aprirlo. Sostituire quindi il `OnAppearing` metodo con il codice seguente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note archiviate nel database.

    Salvare le modifiche apportate a **NotesPage.XAML.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

10. Nella **riquadro della soluzione**fare doppio clic su **NoteEntryPage.XAML.cs** per aprirlo. Sostituire quindi i `OnSaveButtonClicked` metodi `OnDeleteButtonClicked` e con il codice seguente:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      Archivia un' `Note` istanza di che rappresenta una singola nota nell'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. `NoteEntryPage` Quando viene `OnSaveButtonClicked` eseguito il gestore eventi, l' `Note` istanza viene salvata nel database e l'applicazione torna alla pagina precedente. Quando viene `OnDeleteButtonClicked` eseguito il gestore eventi, l' `Note` istanza viene eliminata dal database e l'applicazione torna alla pagina precedente.

      Salvare le modifiche apportate a **NoteEntryPage.XAML.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

11. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il **+** pulsante per passare al **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere un numero di note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come:

- Usare Gestione pacchetti NuGet per aggiungere un pacchetto NuGet a un progetto.
- Archiviare i dati in locale in un database SQLite.NET.

Per applicare uno stile all'applicazione con gli stili XAML, passare alla Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [avanti](styling.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Approfondimento sulla Guida introduttiva a Novell. Forms](deepdive.md)
