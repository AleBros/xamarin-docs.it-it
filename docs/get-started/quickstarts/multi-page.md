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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653789"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Eseguire lo spostamento in un'applicazione Novell. Forms a più pagine

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

In questa Guida introduttiva si apprenderà come:

- Aggiungere altre pagine a una soluzione Novell. Forms.
- Eseguire lo spostamento tra le pagine.
- Utilizzare data binding per sincronizzare i dati tra gli elementi dell'interfaccia utente e la relativa origine dati.

Questa Guida introduttiva illustra come trasformare un'applicazione Novell. Forms multipiattaforma a pagina singola, in grado di archiviare una singola nota, in un'applicazione a più pagine, in grado di archiviare più note. Il risultato è riportato di seguito:

[ ![(multi-page-images/screenshots1-sml.png " ")]Pagina note] (multi-page-images/screenshots1.png#lightbox "Pagina note") Nota pagina immissione(multi-page-images/screenshots2.png#lightbox "Nota pagina voce") [ ![(multi-page-images/screenshots2-sml.png " ")]] 


### <a name="prerequisites"></a>Prerequisiti

Prima di provare questa Guida introduttiva, è necessario completare correttamente la [Guida introduttiva precedente](single-page.md) . In alternativa, scaricare l' [esempio di Guida introduttiva precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio. Nella finestra di avvio fare clic sulla soluzione **Note** nell'elenco progetti/soluzioni recenti oppure fare clic su **Apri un progetto o una soluzione**e nella finestra di dialogo **Apri progetto/soluzione** selezionare il file di soluzione per il progetto note:

    ![](multi-page-images/vs/open-solution.png "Aprire il progetto")

2. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere **Aggiungi > nuova cartella**:

    ![](multi-page-images/vs/add-new-item.png "Aggiungere un nuovo elemento")

3. In **Esplora soluzioni**assegnare un nome ai nuovi **modelli**di cartella:

    ![](multi-page-images/vs/name-folder.png "Cartella modelli")

4. In **Esplora soluzioni**selezionare la cartella **modelli** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo elemento...** :

    ![](multi-page-images/vs/add-new-models-file.png "Aggiungere un nuovo file")

5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi C# visivi > classe**, assegnare un nome alla **Nota**del nuovo file, quindi fare clic sul pulsante **Aggiungi** :

    ![](multi-page-images/vs/add-note-class.png "Aggiungi classe nota")

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

    Questa classe definisce un `Note` modello in cui vengono archiviati i dati relativi a ogni nota nell'applicazione.    

    Salvare le modifiche apportate a **note.cs** premendo **CTRL + S**e chiudere il file.

7. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **Notes** e scegliere **Aggiungi > nuovo elemento..** . Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi C# visivi > pagina contenuto > Novell. Forms**, assegnare al nuovo file il nome **NoteEntryPage**e fare clic sul pulsante **Aggiungi** :

    ![](multi-page-images/vs/add-note-entry-page.png "Aggiungere Novell. Forms ContentPage")

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

      Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che [`Editor`](xref:Xamarin.Forms.Editor) è costituita da un oggetto [`Button`](xref:Xamarin.Forms.Button) per l'input di testo e da due istanze che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due `Button` istanze sono disposte orizzontalmente in [`Grid`](xref:Xamarin.Forms.Grid)un oggetto, `Editor` con `Grid` l'oggetto e viene disposto verticalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout)in un oggetto. Inoltre, `Editor` utilizza Data Binding per eseguire l'associazione `Text` alla proprietà del `Note` modello. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

      Questo codice archivia un' `Note` istanza di, che rappresenta una singola nota, nell' [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) oggetto della pagina. Quando viene [`Button`](xref:Xamarin.Forms.Button) premuto il pulsante Salva `OnSaveButtonClicked` , viene eseguito il gestore eventi, il `Editor` quale salva il contenuto di in un nuovo file con un nome file generato in modo casuale o in un file esistente se viene aggiornata una nota. In entrambi i casi, il file è archiviato nella cartella dei dati dell'applicazione locale per l'applicazione. Il metodo torna quindi alla pagina precedente. Quando viene `Button` premuto il pulsante Elimina `OnDeleteButtonClicked` , viene eseguito il gestore dell'evento, che elimina il file, purché esista, e torna alla pagina precedente. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che [`ListView`](xref:Xamarin.Forms.ListView) è costituita da un oggetto e un oggetto. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Il `ListView` USA Data Binding per visualizzare eventuali note recuperate dall'applicazione e la `NoteEntryPage` selezione di una nota passerà alla posizione in cui è possibile modificare la nota. In alternativa, è possibile creare una nuova nota premendo il `ToolbarItem`. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

    Questo codice definisce la funzionalità per l' `NotesPage`oggetto. Quando viene visualizzata la pagina, `OnAppearing` viene eseguito il metodo, che consente [`ListView`](xref:Xamarin.Forms.ListView) di popolare con le eventuali note recuperate dalla cartella dati applicazione locale. Quando viene premuto, il `OnNoteAddedClicked` gestore eventi viene eseguito. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Questo metodo passa `NoteEntryPage`a, impostando l'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di `NoteEntryPage` su una nuova `Note` istanza di. Quando `ListView` viene selezionato un elemento in, viene `OnListViewItemSelected` eseguito il gestore eventi. Questo metodo consente di passare a `NoteEntryPage`, impostando [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) il valore `NoteEntryPage` di sull'istanza `Note` di selezionata. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

    Questo codice aggiunge una dichiarazione dello spazio dei `System.IO` nomi per lo spazio dei nomi e aggiunge una `FolderPath` dichiarazione per una `string`proprietà statica di tipo. La `FolderPath` proprietà viene usata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati di nota. Inoltre, il codice inizializza la `FolderPath` proprietà `App` nel costruttore e inizializza la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) proprietà in modo [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) che ospiti un'istanza di `NotesPage`. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

14. In **Esplora soluzioni**, nel progetto **note** , fare clic con il pulsante destro del mouse su **MainPage. XAML**e scegliere **Elimina**. Nella finestra di dialogo visualizzata premere il pulsante **OK** per rimuovere il file dal disco rigido.

    In questo modo viene rimossa una pagina che non viene più utilizzata.

15. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il **+** pulsante per passare al **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

    Immettere un numero di note, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac. Nella finestra di avvio fare clic su **Apri**e nella finestra di dialogo selezionare il file della soluzione per il progetto note:

    ![](multi-page-images/vsmac/open-solution.png "Aprire la soluzione")

2. Nella **riquadro della soluzione**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuova cartella**:

    ![](multi-page-images/vsmac/add-new-folder.png "Aggiungi nuova cartella")

3. Nella **riquadro della soluzione**assegnare un nome ai nuovi **modelli**di cartella:

    ![](multi-page-images/vsmac/name-folder.png "Cartella modelli")

4. Nella **riquadro della soluzione**selezionare la cartella **modelli** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Aggiungere un nuovo file")

5. Nella finestra di dialogo **nuovo file** selezionare **Generale > classe vuota**, denominare il nuovo file **Nota**e fare clic sul pulsante **nuovo** :

    ![](multi-page-images/vsmac/add-note-class.png "Aggiungi classe nota")

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

    Questa classe definisce un `Note` modello in cui vengono archiviati i dati relativi a ogni nota nell'applicazione.

    Salvare le modifiche apportate a **note.cs** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

7. Nella **riquadro della soluzione**selezionare il progetto **note** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo file.** Nella finestra di dialogo **nuovo file** selezionare **Forms > Forms ContentPage XAML**, assegnare al nuovo file il nome **NoteEntryPage**e fare clic sul pulsante **nuovo** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Aggiungere Novell. Forms ContentPage")

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

      Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che [`Editor`](xref:Xamarin.Forms.Editor) è costituita da un oggetto [`Button`](xref:Xamarin.Forms.Button) per l'input di testo e da due istanze che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due `Button` istanze sono disposte orizzontalmente in [`Grid`](xref:Xamarin.Forms.Grid)un oggetto, `Editor` con `Grid` l'oggetto e viene disposto verticalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout)in un oggetto. Inoltre, `Editor` utilizza Data Binding per eseguire l'associazione `Text` alla proprietà del `Note` modello. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

      Questo codice archivia un' `Note` istanza di, che rappresenta una singola nota, nell' [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) oggetto della pagina. Quando viene [`Button`](xref:Xamarin.Forms.Button) premuto il pulsante Salva `OnSaveButtonClicked` , viene eseguito il gestore eventi, il `Editor` quale salva il contenuto di in un nuovo file con un nome file generato in modo casuale o in un file esistente se viene aggiornata una nota. In entrambi i casi, il file è archiviato nella cartella dei dati dell'applicazione locale per l'applicazione. Il metodo torna quindi alla pagina precedente. Quando viene `Button` premuto il pulsante Elimina `OnDeleteButtonClicked` , viene eseguito il gestore dell'evento, che elimina il file, purché esista, e torna alla pagina precedente. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che [`ListView`](xref:Xamarin.Forms.ListView) è costituita da un oggetto e un oggetto. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Il `ListView` USA Data Binding per visualizzare eventuali note recuperate dall'applicazione e la `NoteEntryPage` selezione di una nota passerà alla posizione in cui è possibile modificare la nota. In alternativa, è possibile creare una nuova nota premendo il `ToolbarItem`. Per ulteriori informazioni su data binding, vedere [Data Binding](deepdive.md#data-binding) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

    Questo codice definisce la funzionalità per l' `NotesPage`oggetto. Quando viene visualizzata la pagina, `OnAppearing` viene eseguito il metodo, che consente [`ListView`](xref:Xamarin.Forms.ListView) di popolare con le eventuali note recuperate dalla cartella dati applicazione locale. Quando viene premuto, il `OnNoteAddedClicked` gestore eventi viene eseguito. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Questo metodo passa `NoteEntryPage`a, impostando l'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di `NoteEntryPage` su una nuova `Note` istanza di. Quando `ListView` viene selezionato un elemento in, viene `OnListViewItemSelected` eseguito il gestore eventi. Questo metodo consente di passare a `NoteEntryPage`, impostando [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) il valore `NoteEntryPage` di sull'istanza `Note` di selezionata. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

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

    Questo codice aggiunge una dichiarazione dello spazio dei `System.IO` nomi per lo spazio dei nomi e aggiunge una `FolderPath` dichiarazione per una `string`proprietà statica di tipo. La `FolderPath` proprietà viene usata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati di nota. Inoltre, il codice inizializza la `FolderPath` proprietà `App` nel costruttore e inizializza la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) proprietà in modo [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) che ospiti un'istanza di `NotesPage`. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nella [Guida introduttiva a Novell. Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

14. Nel **riquadro della soluzione**, nel progetto **note** , fare clic con il pulsante destro del mouse su **MainPage. XAML**e scegliere **Rimuovi**. Nella finestra di dialogo visualizzata premere il pulsante **Elimina** per rimuovere il file dal disco rigido.

    In questo modo viene rimossa una pagina che non viene più utilizzata.

15. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il **+** pulsante per passare al **NoteEntryPage** e immettere una nota. Dopo aver salvato la nota, l'applicazione tornerà a **NotesPage**.

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
