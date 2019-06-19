---
title: Archiviare i dati in un database locale di SQLite.NET
description: Questo articolo illustra come archiviare i dati in un database di SQLite.NET locale.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: ebf0f21ed57b7d436721018abb2dca329b56baa4
ms.sourcegitcommit: 215b507b2e5a44bb023abc2c804c824b1a6190d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67194964"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Store dei dati in un Database locale di SQLite.NET

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

In questa Guida introduttiva si apprenderà come:

- Usare Gestione pacchetti NuGet per aggiungere un pacchetto NuGet a un progetto.
- Store dei dati in locale in un database di SQLite.NET.

La Guida introduttiva illustra come archiviare i dati in un database di SQLite.NET locale. Il risultato è riportato di seguito:

[![](database-images/screenshots1-sml.png "Note sulla pagina")](database-images/screenshots1.png#lightbox "pagina note")
[![](database-images/screenshots2-sml.png "notare voce pagina")](database-images/screenshots2.png#lightbox "nota Pagina iniziale")

### <a name="prerequisites"></a>Prerequisiti

Si consiglia di completare correttamente il [Guida introduttiva precedente](multi-page.md) prima di tentare di questa Guida introduttiva. In alternativa, scaricare il [esempio di Guida introduttiva precedente](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio e aprire la soluzione note.

2. Nelle **Esplora soluzioni**, selezionare la **note** del progetto, fare doppio clic e selezionare **Gestisci pacchetti NuGet...** :

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

4. In **Esplora soluzioni**, nella **note** progetto aprire **Note.cs** nel **modelli** cartella e sostituire l'oggetto esistente del codice con il codice seguente:

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

    Questa classe definisce un `Note` modello a cui verrà archiviati i dati relativi a ogni nota nell'applicazione. Il `ID` proprietà è contrassegnata con `PrimaryKey` e `AutoIncrement` gli attributi per garantire che ogni `Note` istanza nel database di SQLite.NET avrà un id univoco fornito da SQLite.NET.

    Salvare le modifiche apportate a **Note.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

5. Nelle **Esplora soluzioni**, aggiungere una nuova cartella denominata **dati** per il **note** progetto.

6. Nel **Esplora soluzioni**, nella **note** del progetto, aggiungere una nuova classe denominata **NoteDatabase** per il **dati** cartella.

7. Nelle **NoteDatabase.cs**, sostituire il codice esistente con il codice seguente:

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

    Questa classe contiene il codice per creare il database, leggerli da quest'ultimo, scrivere i dati ad esso ed eliminare dati da esso. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `NoteDatabase` accetta il percorso del file di database come argomento. Questo percorso viene fornito mediante il `App` classe nel passaggio successivo.

    Salvare le modifiche apportate a **NoteDatabase.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

8. In **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **App.xaml.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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
            //...
        }
    }
    ```

    Questo codice definisce una `Database` proprietà che crea un nuovo `NoteDatabase` istanza come un singleton, passando il nome del file del database come argomento per il `NoteDatabase` costruttore. Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

9. In **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **NotesPage.xaml.cs** per aprirlo. Sostituire quindi il `OnAppearing` metodo con il codice seguente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Questo codice popola la [ `ListView` ](xref:Xamarin.Forms.ListView) con eventuali note archiviati nel database.

    Salvare le modifiche apportate a **NotesPage.xaml.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

10. Nelle **Esplora soluzioni**, fare doppio clic su **NoteEntryPage.xaml.cs** per aprirlo. Quindi sostituire il `OnSaveButtonClicked` e `OnDeleteButtonClicked` metodi con il codice seguente:

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

      Il `NoteEntryPage` archivia una `Note` istanza, che rappresenta una singola nota, nelle [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando la `OnSaveButtonClicked` viene eseguito il gestore di evento, il `Note` istanza viene salvata nel database e l'applicazione torna alla pagina precedente. Quando la `OnDeleteButtonClicked` viene eseguito il gestore di evento, il `Note` istanza viene eliminata dal database e l'applicazione torna alla pagina precedente.

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** premendo **CTRL + S**e chiudere il file.

11. Compilare ed eseguire il progetto in ciascuna piattaforma. Per altre informazioni, vedere [compilazione la Guida introduttiva](single-page.md#building-the-quickstart).

    Nel **NotesPage** premere il **+** pulsante per passare alla **NoteEntryPage** e immettere una nota. Dopo il salvataggio della nota applicazione verrà visualizzata nuovamente la **NotesPage**.

    Immettere un numero di Lotus notes, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac e aprire il progetto note.

2. Nel **riquadro della soluzione**, selezionare la **note** del progetto, fare doppio clic e selezionare **Aggiungi > Aggiungi pacchetti NuGet...** :

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

4. Nel **riquadro della soluzione**, nella **note** progetto aprire **Note.cs** nel **modelli** cartella e sostituire il codice esistente con il codice seguente codice:

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

    Questa classe definisce un `Note` modello a cui verrà archiviati i dati relativi a ogni nota nell'applicazione. Il `ID` proprietà è contrassegnata con `PrimaryKey` e `AutoIncrement` gli attributi per garantire che ogni `Note` istanza nel database di SQLite.NET avrà un id univoco fornito da SQLite.NET.

    Salvare le modifiche apportate a **Note.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

5. Nel **riquadro della soluzione**, aggiungere una nuova cartella denominata **Data** per il **note** progetto.

6. Nel **riquadro della soluzione**, nella **note** del progetto, aggiungere una nuova classe denominata **NoteDatabase** per il **dati** cartella.

7. Nelle **NoteDatabase.cs**, sostituire il codice esistente con il codice seguente:

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

    Questa classe contiene il codice per creare il database, leggerli da quest'ultimo, scrivere i dati ad esso ed eliminare dati da esso. Il codice usa API SQLite.NET asincrone che spostano le operazioni sul database in thread in background. Inoltre, il costruttore `NoteDatabase` accetta il percorso del file di database come argomento. Questo percorso viene fornito mediante il `App` classe nel passaggio successivo.

    Salvare le modifiche apportate a **NoteDatabase.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

8. Nel **riquadro della soluzione**, nella **note** del progetto, fare doppio clic su **App.xaml.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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
            ...
        }
    }
    ```

    Questo codice definisce una `Database` proprietà che crea un nuovo `NoteDatabase` istanza come un singleton, passando il nome del file del database come argomento per il `NoteDatabase` costruttore. Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

9. Nel **riquadro della soluzione**, nella **note** del progetto, fare doppio clic su **NotesPage.xaml.cs** per aprirlo. Sostituire quindi il `OnAppearing` metodo con il codice seguente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Questo codice popola la [ `ListView` ](xref:Xamarin.Forms.ListView) con eventuali note archiviati nel database.

    Salvare le modifiche apportate a **NotesPage.xaml.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

10. Nel **riquadro della soluzione**, fare doppio clic su **NoteEntryPage.xaml.cs** per aprirlo. Quindi sostituire il `OnSaveButtonClicked` e `OnDeleteButtonClicked` metodi con il codice seguente:

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

      Il `NoteEntryPage` archivia una `Note` istanza, che rappresenta una singola nota, nelle [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando la `OnSaveButtonClicked` viene eseguito il gestore di evento, il `Note` istanza viene salvata nel database e l'applicazione torna alla pagina precedente. Quando la `OnDeleteButtonClicked` viene eseguito il gestore di evento, il `Note` istanza viene eliminata dal database e l'applicazione torna alla pagina precedente.

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

11. Compilare ed eseguire il progetto in ciascuna piattaforma. Per altre informazioni, vedere [compilazione la Guida introduttiva](single-page.md#building-the-quickstart).

    Nel **NotesPage** premere il **+** pulsante per passare alla **NoteEntryPage** e immettere una nota. Dopo il salvataggio della nota applicazione verrà visualizzata nuovamente la **NotesPage**.

    Immettere un numero di Lotus notes, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come:

- Usare Gestione pacchetti NuGet per aggiungere un pacchetto NuGet a un progetto.
- Store dei dati in locale in un database di SQLite.NET.

Per definire lo stile dell'applicazione con gli stili XAML, continuare per la successiva Guida introduttiva.

> [!div class="nextstepaction"]
> [avanti](styling.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Approfondimento di Guida introduttiva di xamarin. Forms](deepdive.md)
