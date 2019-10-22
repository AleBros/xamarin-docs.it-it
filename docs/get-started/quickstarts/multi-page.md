---
title: Eseguire spostamenti in un'applicazione Xamarin.Forms a più pagine
description: Questo articolo illustra come trasformare l'applicazione a pagina singola, in grado di archiviare una singola nota, in un'applicazione a più pagine, in grado di archiviare più note.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68653789"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Eseguire lo spostamento in un'applicazione Novell. Forms a più pagine

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

In questa Guida introduttiva si apprenderà come:

- Aggiungere altre pagine a una soluzione Novell. Forms.
- Eseguire lo spostamento tra le pagine.
- Utilizzare data binding per sincronizzare i dati tra gli elementi dell'interfaccia utente e la relativa origine dati.

Questa Guida introduttiva illustra come trasformare un'applicazione Novell. Forms multipiattaforma a pagina singola, in grado di archiviare una singola nota, in un'applicazione a più pagine, in grado di archiviare più note. Il risultato è riportato di seguito:

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisites

Prima di provare questa Guida introduttiva, è necessario completare correttamente la [Guida introduttiva precedente](single-page.md) . In alternativa, scaricare l' [esempio di Guida introduttiva precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio. Nella finestra di avvio fare clic sulla soluzione **Note** nell'elenco progetti/soluzioni recenti oppure fare clic su **Apri un progetto o una soluzione**e nella finestra di dialogo **Apri progetto/soluzione** selezionare il file di soluzione per il progetto note:

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere **Aggiungi > nuova cartella**:

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. In **Esplora soluzioni**assegnare un nome ai nuovi **modelli**di cartella:

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. In **Esplora soluzioni**selezionare la cartella **modelli** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo elemento...** :

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi C# visivi > classe**, assegnare un nome alla **Nota**del nuovo file, quindi fare clic sul pulsante **Aggiungi** :

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    Verrà aggiunta una classe denominata **Nota** alla cartella **Models** del progetto **Notes** .

6. In **note.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Questa classe definisce un modello di `Note` che archivia i dati relativi a ciascuna nota nell'applicazione.    

    Salvare le modifiche apportate a **note.cs** premendo **CTRL + S**e chiudere il file.

7. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere **Aggiungi > nuovo elemento..** . Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi C# visivi > pagina contenuto > Novell. Forms**, assegnare al nuovo file il nome **NoteEntryPage**e fare clic sul pulsante **Aggiungi** :

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Verrà aggiunta una nuova pagina denominata **NoteEntryPage** alla cartella radice del progetto. Questa pagina sarà la seconda pagina dell'applicazione.

8. In **NoteEntryPage. XAML**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da un [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e da due istanze di [`Button`](xref:Xamarin.Forms.Button) che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due istanze `Button` sono disposte orizzontalmente in un [`Grid`](xref:Xamarin.Forms.Grid), con la `Editor` e la `Grid` disposti verticalmente in un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Inoltre, il `Editor` utilizza data binding per eseguire il binding alla proprietà `Text` del modello `Note`. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage. XAML** premendo **CTRL + S**e chiudere il file.

9. In **NoteEntryPage.XAML.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Questo codice archivia un'istanza di `Note`, che rappresenta una singola nota, nella [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando viene premuto il [`Button`](xref:Xamarin.Forms.Button) **Salva** , viene eseguito il gestore dell'evento `OnSaveButtonClicked`, che salva il contenuto del `Editor` in un nuovo file con un nome file generato in modo casuale o in un file esistente se è in corso l'aggiornamento di una nota. In entrambi i casi, il file è archiviato nella cartella dei dati dell'applicazione locale per l'applicazione. Il metodo torna quindi alla pagina precedente. Quando viene premuto il `Button` **Delete** , viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, che elimina il file, purché esista, e torna alla pagina precedente. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.XAML.cs** premendo **CTRL + S**e chiudere il file.

      > [!WARNING]
      > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

10. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere **Aggiungi > nuovo elemento..** . Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi C# visivi > pagina contenuto > Novell. Forms**, assegnare al nuovo file il nome **NotesPage**e fare clic sul pulsante **Aggiungi** .

      Verrà aggiunta una pagina denominata **NotesPage** alla cartella radice del progetto. Questa pagina sarà la pagina radice dell'applicazione.

11. In **NotesPage. XAML**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da una [`ListView`](xref:Xamarin.Forms.ListView) e una [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). Il `ListView` utilizza data binding per visualizzare eventuali note recuperate dall'applicazione e la selezione di una nota passerà al `NoteEntryPage` in cui è possibile modificare la nota. In alternativa, è possibile creare una nuova nota premendo il `ToolbarItem`. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage. XAML** premendo **CTRL + S**e chiudere il file.

12. In **NotesPage.XAML.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Questo codice definisce la funzionalità per la `NotesPage`. Quando viene visualizzata la pagina, viene eseguito il metodo `OnAppearing`, che popola il [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note recuperate dalla cartella dati dell'applicazione locale. Quando viene premuto il [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) `OnNoteAddedClicked` viene eseguito il gestore eventi. Questo metodo passa alla `NoteEntryPage`, impostando la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del `NoteEntryPage` su una nuova istanza di `Note`. Quando viene selezionato un elemento nella `ListView`, viene eseguito `OnListViewItemSelected` gestore eventi. Questo metodo consente di passare alla `NoteEntryPage`, impostando il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della `NoteEntryPage` sull'istanza di `Note` selezionata. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.XAML.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

13. In **Esplora soluzioni**fare doppio clic su **app.XAML.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Questo codice aggiunge una dichiarazione dello spazio dei nomi per lo spazio dei nomi `System.IO` e aggiunge una dichiarazione per una proprietà `FolderPath` statica di tipo `string`. La proprietà `FolderPath` viene utilizzata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati di nota. Inoltre, il codice inizializza la proprietà `FolderPath` nel costruttore `App` e inizializza la proprietà [`MainPage`](xref:Xamarin.Forms.Application.MainPage) come [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) che ospita un'istanza di `NotesPage`. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

14. In **Esplora soluzioni**, nel progetto **note** , fare clic con il pulsante destro del mouse su **MainPage. XAML**e scegliere **Elimina**. Nella finestra di dialogo visualizzata premere il pulsante **OK** per rimuovere il file dal disco rigido.

    In questo modo viene rimossa una pagina che non viene più utilizzata.

15. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare al **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere un numero di note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac. Nella finestra di avvio fare clic su **Apri**e nella finestra di dialogo selezionare il file della soluzione per il progetto note:

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. Nella **riquadro della soluzione**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuova cartella**:

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. Nella **riquadro della soluzione**assegnare un nome ai nuovi **modelli**di cartella:

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. Nella **riquadro della soluzione**selezionare la cartella **modelli** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. Nella finestra di dialogo **nuovo file** selezionare **Generale > classe vuota**, denominare il nuovo file **Nota**e fare clic sul pulsante **nuovo** :

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    Verrà aggiunta una classe denominata **Nota** alla cartella **Models** del progetto **Notes** .

6. In **note.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Questa classe definisce un modello di `Note` che archivia i dati relativi a ciascuna nota nell'applicazione.

    Salvare le modifiche apportate a **note.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

7. Nella **riquadro della soluzione**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file.** Nella finestra di dialogo **nuovo file** selezionare **Forms > Forms ContentPage XAML**, assegnare al nuovo file il nome **NoteEntryPage**e fare clic sul pulsante **nuovo** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Verrà aggiunta una nuova pagina denominata **NoteEntryPage** alla cartella radice del progetto. Questa pagina sarà la seconda pagina dell'applicazione.

8. In **NoteEntryPage. XAML**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da un [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e da due istanze di [`Button`](xref:Xamarin.Forms.Button) che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due istanze `Button` sono disposte orizzontalmente in un [`Grid`](xref:Xamarin.Forms.Grid), con la `Editor` e la `Grid` disposti verticalmente in un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Inoltre, il `Editor` utilizza data binding per eseguire il binding alla proprietà `Text` del modello `Note`. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage. XAML** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

9. In **NoteEntryPage.XAML.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Questo codice archivia un'istanza di `Note`, che rappresenta una singola nota, nella [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando viene premuto il [`Button`](xref:Xamarin.Forms.Button) **Salva** , viene eseguito il gestore dell'evento `OnSaveButtonClicked`, che salva il contenuto del `Editor` in un nuovo file con un nome file generato in modo casuale o in un file esistente se è in corso l'aggiornamento di una nota. In entrambi i casi, il file è archiviato nella cartella dei dati dell'applicazione locale per l'applicazione. Il metodo torna quindi alla pagina precedente. Quando viene premuto il `Button` **Delete** , viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, che elimina il file, purché esista, e torna alla pagina precedente. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.XAML.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

      > [!WARNING]
      > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

10. Nella **riquadro della soluzione**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file.** Nella finestra di dialogo **nuovo file** selezionare **Forms > Forms ContentPage XAML**, assegnare al nuovo file il nome **NotesPage**e fare clic sul pulsante **nuovo** .

      Verrà aggiunta una pagina denominata **NotesPage** alla cartella radice del progetto. Questa pagina sarà la pagina radice dell'applicazione.

11. In **NotesPage. XAML**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da una [`ListView`](xref:Xamarin.Forms.ListView) e una [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). Il `ListView` utilizza data binding per visualizzare eventuali note recuperate dall'applicazione e la selezione di una nota passerà al `NoteEntryPage` in cui è possibile modificare la nota. In alternativa, è possibile creare una nuova nota premendo il `ToolbarItem`. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage. XAML** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

12. In **NotesPage.XAML.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Questo codice definisce la funzionalità per la `NotesPage`. Quando viene visualizzata la pagina, viene eseguito il metodo `OnAppearing`, che popola il [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note recuperate dalla cartella dati dell'applicazione locale. Quando viene premuto il [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) `OnNoteAddedClicked` viene eseguito il gestore eventi. Questo metodo passa alla `NoteEntryPage`, impostando la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del `NoteEntryPage` su una nuova istanza di `Note`. Quando viene selezionato un elemento nella `ListView`, viene eseguito `OnListViewItemSelected` gestore eventi. Questo metodo consente di passare alla `NoteEntryPage`, impostando il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della `NoteEntryPage` sull'istanza di `Note` selezionata. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.XAML.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Il tentativo di compilare l'applicazione a questo punto genererà errori che verranno corretti nei passaggi successivi.

13. Nella **riquadro della soluzione**fare doppio clic su **app.XAML.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Questo codice aggiunge una dichiarazione dello spazio dei nomi per lo spazio dei nomi `System.IO` e aggiunge una dichiarazione per una proprietà `FolderPath` statica di tipo `string`. La proprietà `FolderPath` viene utilizzata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati di nota. Inoltre, il codice inizializza la proprietà `FolderPath` nel costruttore `App` e inizializza la proprietà [`MainPage`](xref:Xamarin.Forms.Application.MainPage) come [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) che ospita un'istanza di `NotesPage`. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

14. Nel **riquadro della soluzione**, nel progetto **note** , fare clic con il pulsante destro del mouse su **MainPage. XAML**e scegliere **Rimuovi**. Nella finestra di dialogo visualizzata premere il pulsante **Elimina** per rimuovere il file dal disco rigido.

    In questo modo viene rimossa una pagina che non viene più utilizzata.

15. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare al **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere un numero di note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come:

- Aggiungere altre pagine a una soluzione Novell. Forms.
- Eseguire lo spostamento tra le pagine.
- Utilizzare data binding per sincronizzare i dati tra gli elementi dell'interfaccia utente e la relativa origine dati.

Per modificare l'applicazione in modo da archiviare i dati in un database SQLite.NET locale, passare alla Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [avanti](database.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Approfondimento sulla Guida introduttiva a Novell. Forms](deepdive.md)
