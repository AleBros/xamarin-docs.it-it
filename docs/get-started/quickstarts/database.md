---
title: ''
description: ''
zone_pivot_groups: ''
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2864c7edabde18faec06c2c42042168d3e6c9671
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139802"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Archiviare i dati in un database SQLite.NET locale

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

In questo argomento di avvio rapido si apprenderà come:

- Usare Gestione pacchetti NuGet per aggiungere un pacchetto NuGet a un progetto.
- Archiviare i dati in locale in un database SQLite.NET.

Questo argomento di avvio rapido illustra come archiviare i dati in un database SQLite.NET locale. L'applicazione finale è riportata di seguito:

[![](database-images/screenshots1-sml.png "Notes Page")](database-images/screenshots1.png#lightbox "Notes Page")
[![](database-images/screenshots2-sml.png "Note Entry Page")](database-images/screenshots2.png#lightbox "Note Entry Page")

## <a name="prerequisites"></a>Prerequisiti

È consigliabile completare l'argomento di [avvio rapido precedente](multi-page.md) prima di provare con questo. In alternativa, scaricare l'[esempio di avvio rapido precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/) e usarlo come punto di partenza per questo avvio rapido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio e aprire la soluzione Notes.

2. In **Esplora soluzioni** selezionare il progetto **Notes**, fare clic con il pulsante destro del mouse e selezionare **Gestisci pacchetti NuGet**:

    ![](database-images/vs/add-nuget-packages.png "Add NuGet Packages")    

3. In **Gestione pacchetti NuGet** selezionare la scheda **Sfoglia**, cercare il pacchetto NuGet **sqlite-net-pcl**, selezionarlo e fare clic sul pulsante **Installa** per aggiungerlo al progetto:

    ![](database-images/vs/add-package.png "Add Package")

    > [!NOTE]
    > Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:
    > - **Autore/i:** Frank A. Krueger
    > - **ID:** sqlite-net-pcl
    > - **Collegamento a NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Nonostante il nome del pacchetto, questo pacchetto NuGet può essere usato anche nei progetti .NET Standard.

    Il pacchetto verrà usato per integrare le operazioni di database nell'applicazione.

4. In **Esplora soluzioni**, nel progetto **Notes** aprire **Note.cs** nella cartella **Models** e sostituire il codice esistente con il codice seguente:

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

    Questa classe definisce un modello `Note` che archivierà i dati relativi a ogni nota nell'applicazione. La proprietà `ID` è contrassegnata con gli attributi `PrimaryKey` e `AutoIncrement` per garantire che ogni istanza di `Note` nel database SQLite.NET abbia un ID univoco fornito da SQLite.NET.

    Salvare le modifiche apportate a **Note.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

5. In **Esplora soluzioni** aggiungere una nuova cartella denominata **Data** al progetto **Notes**.

6. In **Esplora soluzioni**, nel progetto **Notes** aggiungere una nuova classe denominata **NoteDatabase** alla cartella **Data**.

7. In **NoteDatabase.cs** sostituire il codice esistente con il seguente:

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

    Questa classe contiene il codice per creare il database, leggere i dati dal database, scrivere i dati in esso ed eliminarli. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `NoteDatabase` accetta il percorso del file di database come argomento. Questo percorso verrà fornito dalla classe `App` nel passaggio successivo.

    Salvare le modifiche apportate a **NoteDatabase.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

8. Fare doppio clic su **App.xaml.cs** nel progetto **Notes** in **Esplora soluzioni** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice definisce una proprietà `Database` che crea una nuova istanza di `NoteDatabase` come singleton, passando il nome file del database come argomento al costruttore `NoteDatabase`. Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

9. Fare doppio clic su **NotesPage.xaml.cs** nel progetto **Notes** in **Esplora soluzioni** per aprire il file, quindi sostituire il metodo `OnAppearing` con il codice seguente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note archiviate nel database.

    Salvare le modifiche apportate a **NotesPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

10. In **Esplora soluzioni** fare doppio clic su **NoteEntryPage.xaml.cs** per aprirlo, quindi sostituire i metodi `OnSaveButtonClicked` e `OnDeleteButtonClicked` con il codice seguente:

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

      `NoteEntryPage`Archivia un' `Note` istanza di che rappresenta una singola nota nell'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando viene eseguito il gestore dell'evento `OnSaveButtonClicked`, l'istanza di `Note` viene salvata nel database e l'applicazione torna alla pagina precedente. Quando viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, l'istanza di `Note` viene eliminata dal database e l'applicazione torna alla pagina precedente.

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

11. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere alcune note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac e aprire il progetto Notes.

2. Nel **riquadro della soluzione** selezionare il progetto **Notes**, fare clic con il pulsante destro del mouse e scegliere **Aggiungi > Aggiungi pacchetti NuGet**:

    ![](database-images/vsmac/add-nuget-packages.png "Add NuGet Packages")    

3. Nella finestra **Aggiungi pacchetti** cercare il pacchetto NuGet **sqlite-net-pcl**, selezionarlo e fare clic sul pulsante **Aggiungi pacchetto** per aggiungerlo al progetto:

    ![](database-images/vsmac/add-package.png "Add Package")

    > [!NOTE]
    > Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:
    > - **Autore:** Frank A. Krueger
    > - **ID:** sqlite-net-pcl
    > - **Collegamento a NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Nonostante il nome del pacchetto, questo pacchetto NuGet può essere usato anche nei progetti .NET Standard.

    Il pacchetto verrà usato per integrare le operazioni di database nell'applicazione.

4. Nel **riquadro della soluzione**, nel progetto **Notes** aprire **Note.cs** nella cartella **Models** e sostituire il codice esistente con il codice seguente:

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

    Questa classe definisce un modello `Note` che archivierà i dati relativi a ogni nota nell'applicazione. La proprietà `ID` è contrassegnata con gli attributi `PrimaryKey` e `AutoIncrement` per garantire che ogni istanza di `Note` nel database SQLite.NET abbia un ID univoco fornito da SQLite.NET.

    Salvare le modifiche apportate a **Note.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

5. Nel **riquadro della soluzione** aggiungere una nuova cartella denominata **Data** al progetto **Notes**.

6. Nel **riquadro della soluzione**, nel progetto **Notes** aggiungere una nuova classe denominata **NoteDatabase** alla cartella **Data**.

7. In **NoteDatabase.cs** sostituire il codice esistente con il seguente:

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

    Questa classe contiene il codice per creare il database, leggere i dati dal database, scrivere i dati in esso ed eliminarli. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `NoteDatabase` accetta il percorso del file di database come argomento. Questo percorso verrà fornito dalla classe `App` nel passaggio successivo.

    Salvare le modifiche apportate a **NoteDatabase.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

8. Fare doppio clic su **App.xaml.cs** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice definisce una proprietà `Database` che crea una nuova istanza di `NoteDatabase` come singleton, passando il nome file del database come argomento al costruttore `NoteDatabase`. Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

9. Fare doppio clic su **NotesPage.xaml.cs** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file, quindi sostituire il metodo `OnAppearing` con il codice seguente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note archiviate nel database.

    Salvare le modifiche apportate a **NotesPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

10. Nel **riquadro della soluzione** fare doppio clic su **NoteEntryPage.xaml.cs** per aprirlo, quindi sostituire i metodi `OnSaveButtonClicked` e `OnDeleteButtonClicked` con il codice seguente:

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

      `NoteEntryPage`Archivia un' `Note` istanza di che rappresenta una singola nota nell'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando viene eseguito il gestore dell'evento `OnSaveButtonClicked`, l'istanza di `Note` viene salvata nel database e l'applicazione torna alla pagina precedente. Quando viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, l'istanza di `Note` viene eliminata dal database e l'applicazione torna alla pagina precedente.

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

11. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere alcune note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

- Usare Gestione pacchetti NuGet per aggiungere un pacchetto NuGet a un progetto.
- Archiviare i dati in locale in un database SQLite.NET.

Per definire lo stile dell'applicazione con gli stili XAML, passare all'argomento di avvio rapido successivo.

> [!div class="nextstepaction"]
> [Avanti](styling.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.FormsApprofondimento sulla Guida introduttiva](deepdive.md)
