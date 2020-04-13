---
title: Eseguire spostamenti in un'applicazione Xamarin.Forms a più pagine
description: Questo articolo illustra come trasformare l'applicazione a singola pagina, in grado di archiviare una singola nota, in un'applicazione multipagina, in grado di archiviare più note.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "68653789"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Eseguire spostamenti in un'applicazione Xamarin.Forms multipagina

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

In questo argomento di avvio rapido si apprenderà come:

- Aggiungere altre pagine a una soluzione Xamarin.Forms.
- Eseguire lo spostamento tra le pagine.
- Usare il data binding per sincronizzare i dati tra gli elementi dell'interfaccia utente e la relativa origine dati.

L'argomento di avvio rapido illustra come trasformare un'applicazione Xamarin.Forms multipiattaforma a singola pagina, in grado di archiviare una singola nota, in un'applicazione multipagina, in grado di archiviare più note. L'applicazione finale è riportata di seguito:

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisiti

È consigliabile completare l'argomento di [avvio rapido precedente](single-page.md) prima di provare con questo. In alternativa, scaricare l'[esempio di avvio rapido precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) e usarlo come punto di partenza per questo avvio rapido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio. Nella finestra di avvio fare clic sulla soluzione **Notes** nell'elenco di progetti/soluzioni recenti oppure fare clic su **Apri un progetto o una soluzione** e nella finestra di dialogo **Apri progetto/soluzione** selezionare il file della soluzione per il progetto Notes:

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere **Aggiungi > Nuova cartella**:

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. In **Esplora soluzioni** assegnare alla nuova cartella il nome **Models**:

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. In **Esplora soluzioni** selezionare la cartella **Models**, fare clic con il pulsante destro del mouse e scegliere **Aggiungi > Nuovo elemento**:

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Elementi di Visual C# > Classe**, assegnare al nuovo file il nome **Note** e fare clic sul pulsante **Aggiungi**:

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    Verrà aggiunta una classe denominata **Note** alla cartella **Models** del progetto **Notes**.

6. In **Note.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questa classe definisce un modello `Note` che archivierà i dati relativi a ogni nota nell'applicazione.    

    Salvare le modifiche apportate a **Note.cs** premendo **CTRL+S** e chiudere il file.

7. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere Aggiungi > **nuovo elemento..**. Nella finestra di dialogo **Aggiungi nuovo elemento,** selezionare **Elementi di Visual C, > Xamarin.Forms > Pagina contenuto**, assegnare al nuovo file il nome **NoteEntryPage**e fare clic sul pulsante **Aggiungi:**

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Verrà aggiunta una nuova pagina denominata **NoteEntryPage** alla cartella radice del progetto. Questa pagina sarà la seconda pagina dell'applicazione.

8. In **NoteEntryPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice definisce in modo dichiarativo l'interfaccia [`Editor`](xref:Xamarin.Forms.Editor) utente per la [`Button`](xref:Xamarin.Forms.Button) pagina, costituita da un per l'input di testo, e due istanze che indicano all'applicazione di salvare o eliminare un file. Le `Button` due istanze sono [`Grid`](xref:Xamarin.Forms.Grid)disposte `Editor` orizzontalmente `Grid` in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout), con e disposte verticalmente in un file . L'`Editor` usa inoltre il data binding per eseguire il binding alla proprietà `Text` del modello `Note`. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml** premendo **CTRL+S** e chiudere il file.

9. In **NoteEntryPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice `Note` archivia un'istanza, che [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) rappresenta una singola nota, nella pagina. Quando **Save** [`Button`](xref:Xamarin.Forms.Button) si preme `OnSaveButtonClicked` Salva viene eseguito il gestore eventi, che salva il contenuto di `Editor` un nuovo file con un nome file generato casualmente o in un file esistente se viene aggiornata una nota. In entrambi i casi, il file viene archiviato nella cartella dei dati dell'applicazione locale, quindi il metodo torna alla pagina precedente. Quando si preme `OnDeleteButtonClicked` **il cancilper** `Button` viene eseguito il gestore eventi, che elimina il file, purché esista, e torna alla pagina precedente. Per altre informazioni sulla navigazione, vedere [Navigazione](deepdive.md#navigation) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

      > [!WARNING]
      > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

10. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere Aggiungi > **nuovo elemento..**. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare Elementi di Visual **C, > Xamarin.Forms > pagina**contenuto , assegnare al nuovo file il nome **NotesPage**, quindi fare clic sul pulsante **Aggiungi** .

      Verrà aggiunta una pagina denominata **NotesPage** alla cartella radice del progetto. Questa pagina sarà la pagina radice dell'applicazione.

11. In **NotesPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce in modo dichiarativo l'interfaccia [`ListView`](xref:Xamarin.Forms.ListView) utente [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)per la pagina, costituita da a e a . `ListView` usa il data binding per visualizzare eventuali note recuperate dall'applicazione e, selezionando una nota, si passerà all'elemento `NoteEntryPage` in cui è possibile modificare la nota. In alternativa, è possibile creare una nuova nota premendo `ToolbarItem`. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** premendo **CTRL+S** e chiudere il file.

12. In **NotesPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce le funzionalità per `NotesPage`. Quando viene visualizzata `OnAppearing` la pagina, viene eseguito il metodo, che popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con eventuali note recuperate dalla cartella dei dati dell'applicazione locale. Quando [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) viene premuto l'oggetto , viene eseguito il `OnNoteAddedClicked` gestore eventi. Questo metodo consente `NoteEntryPage`di passare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) a `NoteEntryPage` , `Note` impostando l'oggetto su una nuova istanza. Quando viene selezionato un elemento in `ListView`, viene eseguito il gestore dell'evento `OnListViewItemSelected`. Questo metodo consente `NoteEntryPage`di passare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) all'oggetto , impostando l'oggetto `NoteEntryPage` dell sull'istanza selezionata. `Note` Per altre informazioni sulla navigazione, vedere [Navigazione](deepdive.md#navigation) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

13. In **Esplora soluzioni** fare doppio clic su **App.xaml.cs** per aprirlo, quindi sostituire il codice esistente con il seguente:

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

    Questo codice aggiunge una dichiarazione per lo spazio dei nomi `System.IO` e aggiunge una dichiarazione per una proprietà `FolderPath` statica di tipo `string`. La proprietà `FolderPath` viene usata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati della nota. Inoltre, il codice inizializza `FolderPath` la `App` proprietà nel costruttore [`MainPage`](xref:Xamarin.Forms.Application.MainPage) e inizializza [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) la proprietà `NotesPage`come oggetto che ospita un'istanza di . Per altre informazioni sulla navigazione, vedere [Navigazione](deepdive.md#navigation) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

14. In **Esplora soluzioni**, nel progetto **Notes** fare clic con il pulsante destro del mouse su **MainPage.xaml** e selezionare **Elimina**. Nella finestra di dialogo visualizzata premere il pulsante **OK** per rimuovere il file dal disco rigido.

    In questo modo viene rimossa una pagina non più usata.

15. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere alcune note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac. Nella finestra iniziale fare clic su **Apri** e nella finestra di dialogo selezionare il file della soluzione per il progetto Notes:

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. Nel **riquadro della soluzione** selezionare il progetto **Notes**, fare clic con il pulsante destro del mouse e scegliere **Aggiungi > Nuova cartella**:

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. Nel **riquadro della soluzione** assegnare alla nuova cartella il nome **Models**:

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. Nel **riquadro della soluzione** selezionare la cartella **Models**, fare clic con il pulsante destro del mouse e scegliere **Aggiungi > Nuovo file**:

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. Nella finestra di dialogo **Nuovo file** selezionare **Generale > Classe vuota**, assegnare al nuovo file il nome **Note** e fare clic sul pulsante **Nuovo**:

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    Verrà aggiunta una classe denominata **Note** alla cartella **Models** del progetto **Notes**.

6. In **Note.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questa classe definisce un modello `Note` che archivierà i dati relativi a ogni nota nell'applicazione.

    Salvare le modifiche apportate a **Note.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

7. Nel **riquadro della soluzione**selezionare il progetto **Notes,** fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file...**. Nella finestra di dialogo **Nuovo file** selezionare Moduli > **Forms ContentPage XAML**, assegnare al nuovo file il nome **NoteEntryPage**e fare clic sul pulsante **Nuovo:**

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Verrà aggiunta una nuova pagina denominata **NoteEntryPage** alla cartella radice del progetto. Questa pagina sarà la seconda pagina dell'applicazione.

8. In **NoteEntryPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice definisce in modo dichiarativo l'interfaccia [`Editor`](xref:Xamarin.Forms.Editor) utente per la [`Button`](xref:Xamarin.Forms.Button) pagina, costituita da un per l'input di testo, e due istanze che indicano all'applicazione di salvare o eliminare un file. Le `Button` due istanze sono [`Grid`](xref:Xamarin.Forms.Grid)disposte `Editor` orizzontalmente `Grid` in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout), con e disposte verticalmente in un file . L'`Editor` usa inoltre il data binding per eseguire il binding alla proprietà `Text` del modello `Note`. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

9. In **NoteEntryPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice `Note` archivia un'istanza, che [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) rappresenta una singola nota, nella pagina. Quando **Save** [`Button`](xref:Xamarin.Forms.Button) si preme `OnSaveButtonClicked` Salva viene eseguito il gestore eventi, che salva il contenuto di `Editor` un nuovo file con un nome file generato casualmente o in un file esistente se viene aggiornata una nota. In entrambi i casi, il file viene archiviato nella cartella dei dati dell'applicazione locale, quindi il metodo torna alla pagina precedente. Quando si preme `OnDeleteButtonClicked` **il cancilper** `Button` viene eseguito il gestore eventi, che elimina il file, purché esista, e torna alla pagina precedente. Per altre informazioni sulla navigazione, vedere [Navigazione](deepdive.md#navigation) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

      > [!WARNING]
      > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

10. Nel **riquadro della soluzione**selezionare il progetto **Notes,** fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file...**. Nella finestra di dialogo **Nuovo file** selezionare Moduli > **Forms ContentPage XAML**, assegnare al nuovo file il nome **NotesPage**e fare clic sul pulsante **Nuovo** .

      Verrà aggiunta una pagina denominata **NotesPage** alla cartella radice del progetto. Questa pagina sarà la pagina radice dell'applicazione.

11. In **NotesPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce in modo dichiarativo l'interfaccia [`ListView`](xref:Xamarin.Forms.ListView) utente [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)per la pagina, costituita da a e a . `ListView` usa il data binding per visualizzare eventuali note recuperate dall'applicazione e, selezionando una nota, si passerà all'elemento `NoteEntryPage` in cui è possibile modificare la nota. In alternativa, è possibile creare una nuova nota premendo `ToolbarItem`. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

12. In **NotesPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce le funzionalità per `NotesPage`. Quando viene visualizzata `OnAppearing` la pagina, viene eseguito il metodo, che popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con eventuali note recuperate dalla cartella dei dati dell'applicazione locale. Quando [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) viene premuto l'oggetto , viene eseguito il `OnNoteAddedClicked` gestore eventi. Questo metodo consente `NoteEntryPage`di passare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) a `NoteEntryPage` , `Note` impostando l'oggetto su una nuova istanza. Quando viene selezionato un elemento in `ListView`, viene eseguito il gestore dell'evento `OnListViewItemSelected`. Questo metodo consente `NoteEntryPage`di passare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) all'oggetto , impostando l'oggetto `NoteEntryPage` dell sull'istanza selezionata. `Note` Per altre informazioni sulla navigazione, vedere [Navigazione](deepdive.md#navigation) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

    > [!WARNING]
    > Se si prova a compilare l'applicazione a questo punto, si verificano errori che verranno risolti in passaggi successivi.

13. Nel **riquadro della soluzione** fare doppio clic su **App.xaml.cs** per aprirlo, quindi sostituire il codice esistente con il seguente:

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

    Questo codice aggiunge una dichiarazione per lo spazio dei nomi `System.IO` e aggiunge una dichiarazione per una proprietà `FolderPath` statica di tipo `string`. La proprietà `FolderPath` viene usata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati della nota. Inoltre, il codice inizializza `FolderPath` la `App` proprietà nel costruttore [`MainPage`](xref:Xamarin.Forms.Application.MainPage) e inizializza [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) la proprietà `NotesPage`come oggetto che ospita un'istanza di . Per altre informazioni sulla navigazione, vedere [Navigazione](deepdive.md#navigation) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

14. Nel **riquadro della soluzione**, nel progetto **Notes** fare clic con il pulsante destro del mouse su **MainPage.xaml** e selezionare **Rimuovi**. Nella finestra di dialogo visualizzata premere il pulsante **Elimina** per rimuovere il file dal disco rigido.

    In questo modo viene rimossa una pagina non più usata.

15. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere alcune note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

- Aggiungere altre pagine a una soluzione Xamarin.Forms.
- Eseguire lo spostamento tra le pagine.
- Usare il data binding per sincronizzare i dati tra gli elementi dell'interfaccia utente e la relativa origine dati.

Per modificare l'applicazione in modo da archiviare i dati in un database SQLite.NET locale, passare all'argomento di avvio rapido successivo.

> [!div class="nextstepaction"]
> [Avanti](database.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md)
