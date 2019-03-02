---
title: Creare una singola pagina applicazione xamarin. Forms
description: Questo articolo illustra come creare un'applicazione xamarin. Forms multipiattaforma a pagina singola, che consente di immettere una nota e renderlo persistente nell'archiviazione del dispositivo.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: dddd41f064be5e7840b6b86d86faec8045b476bf
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197225"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Creare un'applicazione xamarin. Forms pagina singola

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

In questa Guida introduttiva si apprenderà come:

- Creare un'applicazione xamarin. Forms multipiattaforma.
- Definire l'interfaccia utente per una pagina tramite eXtensible Application Markup Language (XAML).
- Interagire con elementi dell'interfaccia utente XAML dal codice.

La Guida introduttiva illustra come creare un'applicazione xamarin. Forms multipiattaforma, che consente di immettere una nota e renderlo persistente nell'archiviazione del dispositivo. Il risultato è riportato di seguito:

[![](single-page-images/screenshots-sml.png "Applicazione Notes")](single-page-images/screenshots.png#lightbox "Applicazione Notes")

::: zone pivot="windows"

### <a name="prerequisites"></a>Prerequisiti

- Visual Studio 2017 (versione più recente), con la **sviluppo di applicazioni per dispositivi mobili con .NET** installato il carico di lavoro.
- Conoscenza di C#.
- (facoltativo) Un Mac associato per compilare l'applicazione in iOS.

Per altre informazioni su questi prerequisiti, vedere [installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2017 a un host di compilazione Mac, vedere [associa a Mac per lo sviluppo di xamarin. IOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio"></a>Introduzione a Visual Studio

1. Avviare Visual Studio e nella pagina iniziale fare clic su **Crea nuovo progetto...**  per creare un nuovo progetto:

    ![](single-page-images/vs/new-solution.png "Nuovo progetto")

2. Nella finestra di dialogo **Nuovo progetto** fare clic su **Multipiattaforma**, selezionare il modello **App per dispositivi mobili (Xamarin.Forms)**, impostare il nome su **Notes**, scegliere un percorso adatto per il progetto e fare clic sul pulsante **OK**:

    ![](single-page-images/vs/new-project.png "Modelli di progetto multipiattaforma")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che la soluzione sia denominata **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva alla soluzione.

3. Nella finestra di dialogo **Nuova app multipiattaforma**, fare clic su **App vuota**, selezionare **.NET Standard** come strategia di condivisione del codice e fare clic sul pulsante **OK**:

    ![](single-page-images/vs/new-app.png "Nuova app multipiattaforma")

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione xamarin. Forms](deepdive.md#anatomy-of-a-xamarinforms-application) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

4. Fare doppio clic su **MainPage.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file:

    ![](single-page-images/vs/open-mainpage-xaml.png "Aprire MainPage.xaml")

5. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina nella quale è costituito da un [ `Label` ](xref:Xamarin.Forms.Label) per visualizzare il testo, un' [ `Editor` ](xref:Xamarin.Forms.Editor) per input di testo e due [ `Button` ](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione per salvare o eliminare un file. Le due istanze di `Button` sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid), con `Label`, `Editor` e `Grid` disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout). Per altre informazioni sulla creazione dell'interfaccia utente, vedere [interfaccia utente](deepdive.md#user-interface) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml** premendo **CTRL+S**, e chiudere il file.

6. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **Notes** e fare doppio clic su **MainPage.xaml.cs** per aprirlo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Aprire MainPage.xaml.cs")

7. In **MainPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se presente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor). Quando si seleziona **Salva** premendo [`Button`](xref:Xamarin.Forms.Button), viene eseguito il gestore eventi `OnSaveButtonClicked`, che consente di salvare il contenuto di `Editor` nel file. Se per selezionare **Elimina** si preme `Button`, viene eseguito il gestore eventi `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`. Per altre informazioni sull'interazione dell'utente, vedere [rispondere all'interazione dell'utente](deepdive.md#responding-to-user-interaction) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml.cs** premendo **CTRL+S**, e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio selezionare la voce di menu **Compila > Compila soluzione** o premere F6. La soluzione viene compilata e nella barra di stato di Visual Studio viene visualizzato un messaggio di operazione completata:

      ![](single-page-images/vs/build-succeeded.png "Compilazione completata")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori fino a quando la soluzione non viene compilata correttamente.

2. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione in un emulatore Android:

    ![](single-page-images/vs/android-start.png "Barra degli strumenti di Visual Studio Android")

    [![](single-page-images/vs/notes-android.png "Note nell'emulatore di Android")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni sul modo in cui viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    > [!NOTE]
    > I passaggi seguenti devono essere effettuati solo se si usa un [Mac associato](~/ios/get-started/installation/windows/connecting-to-mac/index.md) che soddisfa i requisiti di sistema per lo sviluppo di Xamarin.Forms.

3. Nella barra degli strumenti di Visual Studio fare clic con il pulsante destro del mouse sul progetto **Notes.iOS** e selezionare **Imposta come progetto di avvio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Impostare iOS come progetto di avvio")

4. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione nel [simulatore iOS remoto](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Barra degli strumenti di Visual Studio iOS")

    [![](single-page-images/vs/notes-ios.png "Note nel simulatore iOS")](single-page-images/vs/notes-ios-large.png#lightbox "note nel simulatore iOS")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni sul modo in cui viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Prerequisiti

- Visual Studio per Mac (versione più recente), con installato il supporto della piattaforma Android e iOS.
- Xcode (versione più recente).
- Conoscenza di C#.

Per altre informazioni su questi prerequisiti, vedere [installazione di Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

1. Avviare Visual Studio per Mac e nella pagina iniziale fare clic su **Nuovo progetto...**  per creare un nuovo progetto:

    ![](single-page-images/vsmac/new-project.png "Nuova soluzione")

2. Nella finestra di dialogo, **Scegli un modello per il nuovo progetto** fare clic su **Multipiattaforma > App**, selezionare il modello **App Forms vuota** e fare clic sul pulsante **Avanti**:

    ![](single-page-images/vsmac/choose-template.png "Scelta di un modello")

3. Nella finestra di dialogo **Configura l'app Forms vuota** denominare la nuova app **Notes**, verificare che sia selezionato il pulsante di opzione **Usa .NET Standard** e fare clic sul pulsante **Avanti**:    

    ![](single-page-images/vsmac/configure-app.png "Configurare l'applicazione Forms")

4. Nella finestra di dialogo **Configura l'app Forms vuota** lasciare i nomi di soluzione e progetto impostati su **Notes**, scegliere un percorso appropriato per il progetto e fare clic sul pulsante **Crea** per creare il progetto:

    ![](single-page-images/vsmac/configure-project.png "Configurare il progetto Forms")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che sia la soluzione che il progetto siano denominati **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva al progetto.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione xamarin. Forms](deepdive.md#anatomy-of-a-xamarinforms-application) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

5. Fare doppio clic su **MainPage.xaml** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file:

    ![](single-page-images/vsmac/mainpage-xaml.png "MainPage.xaml")

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
            <Editor x:Name="editor"
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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina nella quale è costituito da un [ `Label` ](xref:Xamarin.Forms.Label) per visualizzare il testo, un' [ `Editor` ](xref:Xamarin.Forms.Editor) per input di testo e due [ `Button` ](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione per salvare o eliminare un file. Le due istanze di `Button` sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid), con `Label`, `Editor` e `Grid` disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout). Per altre informazioni sulla creazione dell'interfaccia utente, vedere [interfaccia utente](deepdive.md#user-interface) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S**, e chiudere il file.

7. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **Notes** e fare doppio clic su **MainPage.xaml.cs** per aprirlo:

    ![](single-page-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

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
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se presente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor). Quando si seleziona **Salva** premendo [`Button`](xref:Xamarin.Forms.Button), viene eseguito il gestore eventi `OnSaveButtonClicked`, che consente di salvare il contenuto di `Editor` nel file. Se per selezionare **Elimina** si preme `Button`, viene eseguito il gestore eventi `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`. Per altre informazioni sull'interazione dell'utente, vedere [rispondere all'interazione dell'utente](deepdive.md#responding-to-user-interaction) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S**, e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio per Mac, selezionare la voce di menu **Compila > Compila tutto** o premere **&#8984; + B**. I progetti vengono compilati e viene visualizzato un messaggio di operazione completata nella barra degli strumenti di Visual Studio per Mac.

      ![](single-page-images/vsmac/build-successful.png "Compilazione completata")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori finché i progetti non vengono compilati correttamente.

2. Nel **riquadro della soluzione**, selezionare la **Notes.iOS** progetto, pulsante destro del mouse e scegliere **imposta come progetto di avvio**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Impostare iOS come progetto di avvio")

3. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS prescelto:

      ![](single-page-images/vsmac/start.png "Barra degli strumenti di Visual Studio per Mac")

      [![](single-page-images/vsmac/notes-ios.png "Note nel simulatore iOS")](single-page-images/vsmac/notes-ios-large.png#lightbox "note nel simulatore iOS")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni sul modo in cui viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

4. Nel **riquadro della soluzione**, selezionare la **Notes.Droid** progetto, pulsante destro del mouse e scegliere **imposta come progetto di avvio**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Impostare Android come progetto di avvio")

5. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno dell'emulatore Android prescelto:

      [![](single-page-images/vsmac/notes-android.png "Note nell'emulatore di Android")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni sul modo in cui viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come:

- Creare un'applicazione xamarin. Forms multipiattaforma.
- Definire l'interfaccia utente per una pagina tramite eXtensible Application Markup Language (XAML).
- Interagire con elementi dell'interfaccia utente XAML dal codice.

Per attivare questa applicazione a pagina singola in un'applicazione a più pagine, continuare per la successiva Guida introduttiva.

> [!div class="nextstepaction"]
> [avanti](multi-page.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
- [Approfondimento di Guida introduttiva di xamarin. Forms](deepdive.md)
