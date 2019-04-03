---
title: Eseguire spostamenti in un'applicazione Xamarin.Forms a più pagine
description: Questo articolo illustra come attivare l'applicazione a singola pagina, in grado di archiviare una nota singola, in un'applicazione a più pagine, in grado di archiviare più note.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 855962560897789dadba535f69c4a7da42bb4742
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854977"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Eseguire navigazione in un'applicazione xamarin. Forms con più pagine

[![Download esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)

In questa Guida introduttiva si apprenderà come:

- Aggiungere pagine aggiuntive a una soluzione xamarin. Forms.
- Eseguire la navigazione tra le pagine.
- Utilizzare l'associazione dati per sincronizzare i dati tra gli elementi dell'interfaccia utente e l'origine dati utilizzata.

La Guida introduttiva illustra come trasformare lo sviluppo multipiattaforma xamarin. Forms applicazione a singola pagina, in grado di archiviare una nota singola, in un'applicazione a più pagine, in grado di archiviare più note. Il risultato è riportato di seguito:

[![](multi-page-images/screenshots1-sml.png "Note sulla pagina")](multi-page-images/screenshots1.png#lightbox "pagina note")
[![](multi-page-images/screenshots2-sml.png "notare voce pagina")](multi-page-images/screenshots2.png#lightbox "nota Pagina iniziale")

### <a name="prerequisites"></a>Prerequisiti

Si consiglia di completare correttamente il [Guida introduttiva precedente](single-page.md) prima di tentare di questa Guida introduttiva. In alternativa, scaricare il [esempio di Guida introduttiva precedente](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio. Nella finestra di avvio, fare clic sulla **note** soluzione nell'elenco di progetti e soluzioni recenti, o fare clic su **apre un progetto o soluzione**e nel **Apri progetto/soluzione** finestra di dialogo Selezionare il file di soluzione per il progetto note:

    ![](multi-page-images/vs/open-solution.png "Aprire il progetto")

2. Nella **Esplora soluzioni**, fare clic sui **note** del progetto e selezionare **Aggiungi > nuova cartella**:

    ![](multi-page-images/vs/add-new-item.png "Aggiungere un nuovo elemento")

3. Nelle **Esplora soluzioni**, denominare la nuova cartella **modelli**:

    ![](multi-page-images/vs/name-folder.png "Cartella modelli")

4. Nelle **Esplora soluzioni**, selezionare la **modelli** cartella, pulsante destro del mouse e scegliere **Aggiungi > Nuovo elemento...** :

    ![](multi-page-images/vs/add-new-models-file.png "Aggiungere un nuovo file")

5. Nel **Aggiungi nuovo elemento** finestra di dialogo, seleziona **Visual C# elementi > classe**, denominare il nuovo file **nota**, fare clic sul **Aggiungi** pulsante:

    ![](multi-page-images/vs/add-note-class.png "Aggiungi classe nota")

    Verrà aggiunta una classe denominata **nota** per il **modelli** cartella della **note** progetto.

6. Nelle **Note.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questa classe definisce un `Note` modello a cui verrà archiviati i dati relativi a ogni nota nell'applicazione.    

    Salvare le modifiche apportate a **Note.cs** premendo **CTRL + S**e chiudere il file.

7. Nella **Esplora soluzioni**, fare clic sul **note** del progetto e selezionare **Aggiungi > Nuovo elemento...** . Nel **Aggiungi nuovo elemento** finestra di dialogo, seleziona **Visual C# elementi > xamarin. Forms > pagina contenuto**, denominare il nuovo file **NoteEntryPage**, fare clic sul  **Aggiungere** pulsante:

    ![](multi-page-images/vs/add-note-entry-page.png "Aggiungere xamarin. Forms ContentPage")

    Verrà aggiunta una nuova pagina denominata **NoteEntryPage** nella cartella radice del progetto. Questa pagina sarà la seconda pagina nell'applicazione.

8. Nelle **NoteEntryPage.xaml**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina nella quale è costituito da un [ `Editor` ](xref:Xamarin.Forms.Editor) per input di testo e due [ `Button` ](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione per salvare o eliminare un file. I due `Button` istanze sono disposti orizzontalmente un [ `Grid` ](xref:Xamarin.Forms.Grid), con il `Editor` e `Grid` verticalmente venga inserita in un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Inoltre, il `Editor` Usa l'associazione dati da associare al `Text` proprietà del `Note` modello. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml** premendo **CTRL + S**e chiudere il file.

9. Nelle **NoteEntryPage.xaml.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice archivia una `Note` istanza, che rappresenta una singola nota, nelle [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando la **salvare** [ `Button` ](xref:Xamarin.Forms.Button) viene premuto il `OnSaveButtonClicked` gestore eventi viene eseguito, quale uno Salva il contenuto del `Editor` in un nuovo file con un nome file generato casualmente, o in un file esistente se viene aggiornata una nota. In entrambi i casi, il file viene archiviato nella cartella dati applicazioni locale per l'applicazione. Il metodo quindi torna alla pagina precedente. Quando la **eliminare** `Button` viene premuto il `OnDeleteButtonClicked` viene eseguito il gestore eventi, che consente di eliminare il file, condizione che esista e che consente di passare alla pagina precedente,. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** premendo **CTRL + S**e chiudere il file.

      > [!WARNING]
      > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

10. Nella **Esplora soluzioni**, fare clic sul **note** del progetto e selezionare **Aggiungi > Nuovo elemento...** . Nel **Aggiungi nuovo elemento** finestra di dialogo, seleziona **Visual C# elementi > xamarin. Forms > pagina contenuto**, denominare il nuovo file **NotesPage**, fare clic sul **Aggiungi**  pulsante.

      Verrà aggiunta una pagina denominata **NotesPage** nella cartella radice del progetto. Questa pagina sarà la pagina principale dell'applicazione.

11. Nelle **NotesPage.xaml**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina nella quale è costituito da un [ `ListView` ](xref:Xamarin.Forms.ListView) e un [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). Il `ListView` Usa i dati di associazione per visualizzare eventuali note che vengono recuperati dall'applicazione e selezionando una nota per passare al `NoteEntryPage` dove la nota può essere modificata. In alternativa, è possibile creare una nuova nota premendo il `ToolbarItem`. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** premendo **CTRL + S**e chiudere il file.

12. Nelle **NotesPage.xaml.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce le funzionalità per il `NotesPage`. Quando viene visualizzata la pagina, il `OnAppearing` metodo viene eseguito, che popola le [ `ListView` ](xref:Xamarin.Forms.ListView) con eventuali note che sono stati recuperati dalla cartella di dati dell'applicazione locale. Quando la [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) viene premuto il `OnNoteAddedClicked` viene eseguito il gestore eventi. Questo metodo consente di passare al `NoteEntryPage`, impostando il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del `NoteEntryPage` in un nuovo `Note` istanza. Quando un elemento il `ListView` sia selezionata la `OnListViewItemSelected` viene eseguito il gestore eventi. Questo metodo passa al `NoteEntryPage`, impostando il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del `NoteEntryPage` al `Note` istanza. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml.cs** premendo **CTRL + S**e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

13. Nelle **Esplora soluzioni**, fare doppio clic su **App.xaml.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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
            ...
        }
    }
    ```

    Questo codice aggiunge una dichiarazione dello spazio dei nomi per il `System.IO` dello spazio dei nomi e aggiunge una dichiarazione per un valore statico `FolderPath` vlastnosti typu `string`. Il `FolderPath` proprietà viene utilizzata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati. Inoltre, il codice inizializza il `FolderPath` proprietà nel `App` costruttore e inizializza il [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) della proprietà un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) che ospita un istanza di `NotesPage`. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

14. Nella **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **MainPage. XAML**e selezionare **Elimina**. Nella finestra di dialogo che viene visualizzato premere il **OK** pulsante per rimuovere il file dal disco rigido.

    Questa operazione rimuove una pagina che non viene più utilizzata.

15. Compilare ed eseguire il progetto in ciascuna piattaforma. Per altre informazioni, vedere [compilazione la Guida introduttiva](single-page.md#building-the-quickstart).

    Nel **NotesPage** premere il **+** pulsante per passare alla **NoteEntryPage** e immettere una nota. Dopo il salvataggio della nota applicazione verrà visualizzata nuovamente la **NotesPage**.

    Immettere un numero di Lotus notes, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac. Nella finestra di avvio fare clic su **aperto**e nella finestra di dialogo selezionare il file di soluzione per il progetto note:

    ![](multi-page-images/vsmac/open-solution.png "Aprire la soluzione")

2. Nel **riquadro della soluzione**, selezionare la **note** progetto, pulsante destro del mouse e scegliere **Aggiungi > nuova cartella**:

    ![](multi-page-images/vsmac/add-new-folder.png "Aggiungi nuova cartella")

3. Nel **riquadro della soluzione**, denominare la nuova cartella **modelli**:

    ![](multi-page-images/vsmac/name-folder.png "Cartella modelli")

4. Nel **riquadro della soluzione**, selezionare la **modelli** cartella, pulsante destro del mouse e scegliere **Aggiungi > Nuovo File...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Aggiungere un nuovo file")

5. Nel **nuovo File** finestra di dialogo, seleziona **generale > classe vuota**, denominare il nuovo file **nota**, fare clic sul **New** pulsante:

    ![](multi-page-images/vsmac/add-note-class.png "Aggiungi classe nota")

    Verrà aggiunta una classe denominata **nota** per il **modelli** cartella della **note** progetto.

6. Nelle **Note.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questa classe definisce un `Note` modello a cui verrà archiviati i dati relativi a ogni nota nell'applicazione.

    Salvare le modifiche apportate a **Note.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

7. Nel **riquadro della soluzione**, selezionare la **note** progetto, pulsante destro del mouse e scegliere **Aggiungi > Nuovo File...** . Nel **nuovo File** finestra di dialogo, seleziona **Forms > Forms ContentPage XAML**, denominare il nuovo file **NoteEntryPage**e fare clic sui **New** pulsante:

    ![](multi-page-images/vsmac/add-note-entry-page.png "Aggiungere xamarin. Forms ContentPage")

    Verrà aggiunta una nuova pagina denominata **NoteEntryPage** nella cartella radice del progetto. Questa pagina sarà la seconda pagina nell'applicazione.

8. Nelle **NoteEntryPage.xaml**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina nella quale è costituito da un [ `Editor` ](xref:Xamarin.Forms.Editor) per input di testo e due [ `Button` ](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione per salvare o eliminare un file. I due `Button` istanze sono disposti orizzontalmente un [ `Grid` ](xref:Xamarin.Forms.Grid), con il `Editor` e `Grid` verticalmente venga inserita in un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Inoltre, il `Editor` Usa l'associazione dati da associare al `Text` proprietà del `Note` modello. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

9. Nelle **NoteEntryPage.xaml.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

      Questo codice archivia una `Note` istanza, che rappresenta una singola nota, nelle [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina. Quando la **salvare** [ `Button` ](xref:Xamarin.Forms.Button) viene premuto il `OnSaveButtonClicked` gestore eventi viene eseguito, quale uno Salva il contenuto del `Editor` in un nuovo file con un nome file generato casualmente, o in un file esistente se viene aggiornata una nota. In entrambi i casi, il file viene archiviato nella cartella dati applicazioni locale per l'applicazione. Il metodo quindi torna alla pagina precedente. Quando la **eliminare** `Button` viene premuto il `OnDeleteButtonClicked` viene eseguito il gestore eventi, che consente di eliminare il file, condizione che esista e che consente di passare alla pagina precedente,. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

      Salvare le modifiche apportate a **NoteEntryPage.xaml.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

      > [!WARNING]
      > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

10. Nel **riquadro della soluzione**, selezionare la **note** progetto, pulsante destro del mouse e scegliere **Aggiungi > Nuovo File...** . Nel **nuovo File** finestra di dialogo, seleziona **Forms > Forms ContentPage XAML**, denominare il nuovo file **NotesPage**e fare clic sui **New** pulsante.

      Verrà aggiunta una pagina denominata **NotesPage** nella cartella radice del progetto. Questa pagina sarà la pagina principale dell'applicazione.

11. Nelle **NotesPage.xaml**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina nella quale è costituito da un [ `ListView` ](xref:Xamarin.Forms.ListView) e un [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). Il `ListView` Usa i dati di associazione per visualizzare eventuali note che vengono recuperati dall'applicazione e selezionando una nota per passare al `NoteEntryPage` dove la nota può essere modificata. In alternativa, è possibile creare una nuova nota premendo il `ToolbarItem`. Per altre informazioni sul data binding, vedere [Data binding](deepdive.md#data-binding) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

12. Nelle **NotesPage.xaml.cs**rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce le funzionalità per il `NotesPage`. Quando viene visualizzata la pagina, il `OnAppearing` metodo viene eseguito, che popola le [ `ListView` ](xref:Xamarin.Forms.ListView) con eventuali note che sono stati recuperati dalla cartella di dati dell'applicazione locale. Quando la [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) viene premuto il `OnNoteAddedClicked` viene eseguito il gestore eventi. Questo metodo consente di passare al `NoteEntryPage`, impostando il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del `NoteEntryPage` in un nuovo `Note` istanza. Quando un elemento il `ListView` sia selezionata la `OnListViewItemSelected` viene eseguito il gestore eventi. Questo metodo passa al `NoteEntryPage`, impostando il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del `NoteEntryPage` al `Note` istanza. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml.cs** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

    > [!WARNING]
    > Provare a compilare l'applicazione a questo punto si verificheranno errori che verranno risolti nei passaggi successivi.

13. Nel **riquadro della soluzione**, fare doppio clic su **App.xaml.cs** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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
            ...
        }
    }
    ```

    Questo codice aggiunge una dichiarazione dello spazio dei nomi per il `System.IO` dello spazio dei nomi e aggiunge una dichiarazione per un valore statico `FolderPath` vlastnosti typu `string`. Il `FolderPath` proprietà viene utilizzata per archiviare il percorso nel dispositivo in cui verranno archiviati i dati. Inoltre, il codice inizializza il `FolderPath` proprietà nel `App` costruttore e inizializza il [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) della proprietà un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) che ospita un istanza di `NotesPage`. Per altre informazioni sulla navigazione, vedere [navigazione](deepdive.md#navigation) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

14. Nel **riquadro della soluzione**, nella **note** del progetto, fare doppio clic su **MainPage. XAML**e selezionare **rimuovere**. Nella finestra di dialogo che viene visualizzato premere il **eliminare** pulsante per rimuovere il file dal disco rigido.

    Questa operazione rimuove una pagina che non viene più utilizzata.

15. Compilare ed eseguire il progetto in ciascuna piattaforma. Per altre informazioni, vedere [compilazione la Guida introduttiva](single-page.md#building-the-quickstart).

    Nel **NotesPage** premere il **+** pulsante per passare alla **NoteEntryPage** e immettere una nota. Dopo il salvataggio della nota applicazione verrà visualizzata nuovamente la **NotesPage**.

    Immettere un numero di Lotus notes, di lunghezza variabile, per osservare il comportamento dell'applicazione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come:

- Aggiungere pagine aggiuntive a una soluzione xamarin. Forms.
- Eseguire la navigazione tra le pagine.
- Utilizzare l'associazione dati per sincronizzare i dati tra gli elementi dell'interfaccia utente e l'origine dati utilizzata.

Per modificare l'applicazione in modo che archivia i dati in un database locale di SQLite.NET, continuare per la successiva Guida introduttiva.

> [!div class="nextstepaction"]
> [Successivo](database.md)

## <a name="related-links"></a>Collegamenti correlati

- [Note (esempio)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)
- [Approfondimento di Guida introduttiva di xamarin. Forms](deepdive.md)
