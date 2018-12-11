---
title: Guida introduttiva di Xamarin.Forms
description: Questo articolo illustra come creare con Xamarin.Forms una semplice applicazione multipiattaforma denominata Notes, che consente di immettere una nota e di renderla persistente nell'archiviazione del dispositivo.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/21/2018
ms.openlocfilehash: 880fa527d91098cf8c5f8c46cd9c5cce9ec9a489
ms.sourcegitcommit: 744c0a50420bb091fca8b92a84c20e61c741cf9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52743140"
---
# <a name="xamarinforms-quickstart"></a>Guida introduttiva di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

Questa guida introduttiva spiega in dettaglio come creare con Xamarin.Forms una semplice applicazione multipiattaforma denominata Notes, che consente di immettere una nota e di renderla persistente nell'archiviazione del dispositivo. Il risultato è riportato di seguito:

[![](quickstart-experimental-images/screenshots-sml.png "Applicazione Notes")](quickstart-experimental-images/screenshots.png#lightbox "Applicazione Notes")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Introduzione a Visual Studio

1. Nella schermata **Start**, avviare Visual Studio. Verrà aperta la pagina iniziale:

    ![](quickstart-experimental-images/vs/start-page.png "Visual Studio")

2. In Visual Studio, fare clic su **Crea nuovo progetto...** per creare un nuovo progetto:

    ![](quickstart-experimental-images/vs/new-solution.png "Nuovo progetto")

3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Multipiattaforma**, selezionare il modello **App per dispositivi mobili (Xamarin.Forms)**, impostare il nome su **Notes**, scegliere un percorso adatto per il progetto e fare clic sul pulsante **OK**:

    ![](quickstart-experimental-images/vs/new-project.png "Modelli di progetto multipiattaforma")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che la soluzione sia denominata **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva alla soluzione.

4. Nella finestra di dialogo **Nuova app multipiattaforma**, fare clic su **App vuota**, selezionare **.NET Standard** come strategia di condivisione del codice e fare clic sul pulsante **OK**:

    ![](quickstart-experimental-images/vs/new-app.png "Nuova app multipiattaforma")

5. Fare doppio clic su **MainPage.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file:

    ![](quickstart-experimental-images/vs/open-mainpage-xaml.png "Aprire MainPage.xaml")

6. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, costituita da un oggetto [`Label`](xref:Xamarin.Forms.Label) per visualizzare il testo, un oggetto [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e due istanze di [`Button`](xref:Xamarin.Forms.Button) che indirizzano l'applicazione in modo da salvare o eliminare un file. Le due istanze di `Button` sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid), con `Label`, `Editor` e `Grid` disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Salvare le modifiche apportate a **MainPage.xaml** premendo **CTRL+S**, e chiudere il file.

7. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **Notes** e fare doppio clic su **MainPage.xaml.cs** per aprirlo:

    ![](quickstart-experimental-images/vs/open-mainpage-codebehind.png "Aprire MainPage.xaml.cs")

8. In **MainPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se presente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor). Quando si seleziona **Salva** premendo [`Button`](xref:Xamarin.Forms.Button), viene eseguito il gestore eventi `OnSaveButtonClicked`, che consente di salvare il contenuto di `Editor` nel file. Se per selezionare **Elimina** si preme `Button`, viene eseguito il gestore eventi `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`.

    Salvare le modifiche apportate a **MainPage.xaml.cs** premendo **CTRL+S**, e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio selezionare la voce di menu **Compila > Compila soluzione** o premere F6. La soluzione viene compilata e nella barra di stato di Visual Studio viene visualizzato un messaggio di operazione completata:

      ![](quickstart-experimental-images/vs/build-succeeded.png "Compilazione completata")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori fino a quando la soluzione non viene compilata correttamente.

2. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione in un emulatore Android:

    ![](quickstart-experimental-images/vs/android-start.png "Barra degli strumenti di Visual Studio Android")

    ![](quickstart-experimental-images/vs/notes-android.png "Notes nell'emulatore Android")

    Immettere una nota e premere il pulsante **Salva**.

    > [!NOTE]
    > I passaggi seguenti devono essere effettuati solo se si usa un [Mac associato](~/ios/get-started/installation/windows/connecting-to-mac/index.md) che soddisfa i requisiti di sistema per lo sviluppo di Xamarin.Forms.

3. Nella barra degli strumenti di Visual Studio fare clic con il pulsante destro del mouse sul progetto **Notes.iOS** e selezionare **Imposta come progetto di avvio**.

      ![](quickstart-experimental-images/vs/set-as-startup-project-ios.png "Impostare iOS come progetto di avvio")

4. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione nel [simulatore iOS remoto](~/tools/ios-simulator/index.md):

    ![](quickstart-experimental-images/vs/ios-start.png "Barra degli strumenti di Visual Studio iOS")

    ![](quickstart-experimental-images/vs/notes-ios.png "Notes nel simulatore iOS")

    Immettere una nota e premere il pulsante **Salva**.

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

1. Avviare Visual Studio per Mac e nella pagina iniziale fare clic su **Nuovo progetto...**  per creare un nuovo progetto:

    ![](quickstart-experimental-images/vsmac/new-project.png "Nuova soluzione")

2. Nella finestra di dialogo, **Scegli un modello per il nuovo progetto** fare clic su **Multipiattaforma > App**, selezionare il modello **App Forms vuota** e fare clic sul pulsante **Avanti**:

    ![](quickstart-experimental-images/vsmac/choose-template.png "Scelta di un modello")

3. Nella finestra di dialogo **Configura l'app Forms vuota** denominare la nuova app **Notes**, verificare che sia selezionato il pulsante di opzione **Usa .NET Standard** e fare clic sul pulsante **Avanti**:    

    ![](quickstart-experimental-images/vsmac/configure-app.png "Configurare l'applicazione Forms")

4. Nella finestra di dialogo **Configura l'app Forms vuota** lasciare i nomi di soluzione e progetto impostati su **Notes**, scegliere un percorso appropriato per il progetto e fare clic sul pulsante **Crea** per creare il progetto:

    ![](quickstart-experimental-images/vsmac/configure-project.png "Configurare il progetto Forms")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che sia la soluzione che il progetto siano denominati **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva al progetto.

5. Fare doppio clic su **MainPage.xaml** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, costituita da un oggetto [`Label`](xref:Xamarin.Forms.Label) per visualizzare il testo, un oggetto [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e due istanze di [`Button`](xref:Xamarin.Forms.Button) che indirizzano l'applicazione in modo da salvare o eliminare un file. Le due istanze di `Button` sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid), con `Label`, `Editor` e `Grid` disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Salvare le modifiche apportate a **MainPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S**, e chiudere il file.

7. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **Notes** e fare doppio clic su **MainPage.xaml.cs** per aprirlo:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. In **MainPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se presente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor). Quando si seleziona **Salva** premendo [`Button`](xref:Xamarin.Forms.Button), viene eseguito il gestore eventi `OnSaveButtonClicked`, che consente di salvare il contenuto di `Editor` nel file. Se per selezionare **Elimina** si preme `Button`, viene eseguito il gestore eventi `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`.

    Salvare le modifiche apportate a **MainPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S**, e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio per Mac, selezionare la voce di menu **Compila > Compila tutto** o premere **&#8984; + B**. I progetti vengono compilati e viene visualizzato un messaggio di operazione completata nella barra degli strumenti di Visual Studio per Mac.

      ![](quickstart-experimental-images/vsmac/build-successful.png "Compilazione completata")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori finché i progetti non vengono compilati correttamente.

2. Nel **riquadro della soluzione** selezionare il progetto **Notes.iOS**, fare clic con il pulsante destro del mouse e selezionare **Imposta come progetto di avvio**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-ios.png "Impostare iOS come progetto di avvio")

3. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS prescelto:

      ![](quickstart-experimental-images/vsmac/start.png "Barra degli strumenti di Visual Studio per Mac")

      ![](quickstart-experimental-images/vsmac/notes-ios.png "Notes nel simulatore iOS")

    Immettere una nota e premere il pulsante **Salva**.

4. Nel **riquadro della soluzione** selezionare il progetto **Notes.Droid**, fare clic con il pulsante destro del mouse e selezionare **Imposta come progetto di avvio**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-android.png "Impostare Android come progetto di avvio")

5. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno dell'emulatore Android prescelto:

      ![](quickstart-experimental-images/vsmac/notes-android.png "Notes nell'emulatore Android")

    Immettere una nota e premere il pulsante **Salva**.

::: zone-end

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
