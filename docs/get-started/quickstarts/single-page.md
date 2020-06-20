---
title: Creare un'applicazione a pagina singola Xamarin.Forms
description: Questo articolo illustra come creare un'applicazione multipiattaforma a pagina singola Xamarin.Forms , che consente di immettere una nota e di renderla permanente nell'archiviazione del dispositivo.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b9a3017fc8188d3669b64d95c968b2d0a5325358
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136071"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Creare un'applicazione a pagina singola Xamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

In questo argomento di avvio rapido si apprenderà come:

- Creare un'applicazione multipiattaforma Xamarin.Forms .
- Definire l'interfaccia utente per una pagina usando eXtensible Application Markup Language (XAML).
- Interagire con gli elementi dell'interfaccia utente XAML dal codice.

Questa Guida introduttiva illustra come creare un'applicazione multipiattaforma Xamarin.Forms , che consente di immettere una nota e di renderla permanente nell'archiviazione del dispositivo. L'applicazione finale è riportata di seguito:

[![](single-page-images/screenshots-sml.png "Notes Application")](single-page-images/screenshots.png#lightbox "Notes Application")

::: zone pivot="windows"

### <a name="prerequisites"></a>Prerequisiti

- Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**.
- Conoscenza di C#.
- (facoltativo) Un Mac associato per compilare l'applicazione in iOS.

Per altre informazioni su questi prerequisiti, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introduzione a Visual Studio 2019

1. Avviare Visual Studio 2019 e nella finestra iniziale fare clic su **Crea un nuovo progetto** per creare un nuovo progetto:

    ![](single-page-images/vs/new-solution-2019.png "New Project")

2. Nella finestra **Crea un nuovo progetto** selezionare **mobile** nell'elenco a discesa **tipo di progetto** , selezionare il modello **app mobile ( Xamarin.Forms )** e fare clic sul pulsante **Avanti** :

    ![](single-page-images/vs/new-project-2019.png "Cross-Platform Project Templates")

3. Nella finestra **Configura il nuovo progetto** impostare **Nome progetto** su **Notes**, scegliere un percorso appropriato per il progetto e fare clic sul pulsante **Crea**:

    ![](single-page-images/vs/configure-project.png "Configure your Project")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che la soluzione sia denominata **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva alla soluzione.

4. Nella finestra di dialogo **Nuova app multipiattaforma** fare clic su **App vuota** e quindi sul pulsante **OK**:

    ![](single-page-images/vs/new-app-2019.png "New Cross-Platform App")

    Per ulteriori informazioni sulla libreria di .NET Standard creata, vedere la pagina relativa all' [anatomia di un' Xamarin.Forms applicazione](deepdive.md#anatomy-of-a-xamarinforms-application) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

5. Fare doppio clic su **MainPage.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file:

    ![](single-page-images/vs/open-mainpage-xaml-2019.png "Open MainPage.xaml")

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da un oggetto [`Label`](xref:Xamarin.Forms.Label) per visualizzare il testo, un oggetto [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e due [`Button`](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due `Button` istanze sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid) , con l'oggetto, `Label` `Editor` e `Grid` vengono disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Per ulteriori informazioni sulla creazione dell'interfaccia utente, vedere [interfaccia utente](deepdive.md#user-interface) nell' [ Xamarin.Forms esercitazione approfondita di avvio rapido](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml** premendo **CTRL+S** e chiudere il file.

7. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **Notes** e fare doppio clic su **MainPage.xaml.cs** per aprirlo:

    ![](single-page-images/vs/open-mainpage-codebehind-2019.png "Open MainPage.xaml.cs")

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

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se esistente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor) . Quando **Save** [`Button`](xref:Xamarin.Forms.Button) viene premuto il pulsante Salva `OnSaveButtonClicked` , viene eseguito il gestore eventi, il quale salva il contenuto di nel `Editor` file. Se per selezionare **Elimina si preme ** `Button`, viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`. Per ulteriori informazioni sull'interazione dell'utente, vedere [risposta all'interazione dell'utente](deepdive.md#responding-to-user-interaction) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio selezionare la voce di menu **Compila > Compila soluzione** o premere F6. La soluzione viene compilata e nella barra di stato di Visual Studio viene visualizzato un messaggio di operazione completata:

      ![](single-page-images/vs/build-succeeded.png "Build Succeeded")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori fino a quando la soluzione non viene compilata correttamente.

2. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione in un emulatore Android:

    ![](single-page-images/vs/android-start.png "Visual Studio Android Toolbar")

    [![](single-page-images/vs/notes-android.png "Notes in the Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni su come viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

    > [!NOTE]
    > La procedura seguente deve essere eseguita solo se si dispone di un [Mac associato](~/ios/get-started/installation/windows/connecting-to-mac/index.md) che soddisfa i requisiti di sistema per Xamarin.Forms lo sviluppo.

3. Nella barra degli strumenti di Visual Studio fare clic con il pulsante destro del mouse sul progetto **Notes.iOS** e selezionare **Imposta come progetto di avvio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Set iOS as Startup Project")

4. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione nel [simulatore iOS remoto](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Visual Studio iOS Toolbar")

    [![](single-page-images/vs/notes-ios.png "Notes in the iOS Simulator")](single-page-images/vs/notes-ios-large.png#lightbox "Notes in the iOS Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni su come viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Prerequisiti

- Visual Studio 2017 con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**.
- Conoscenza di C#.
- (facoltativo) Un Mac associato per compilare l'applicazione in iOS.

Per altre informazioni su questi prerequisiti, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Introduzione a Visual Studio 2017

1. Avviare Visual Studio 2017 e nella pagina iniziale fare clic su **Crea nuovo progetto** per creare un nuovo progetto:

    ![](single-page-images/vs/new-solution.png "New Project")

2. Nella finestra di dialogo **nuovo progetto** fare clic su **multipiattaforma**, selezionare il modello **app per dispositivi mobili ( Xamarin.Forms )** , impostare il nome su **Note**, scegliere un percorso appropriato per il progetto e fare clic sul pulsante **OK** :

    ![](single-page-images/vs/new-project.png "Cross-Platform Project Templates")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che la soluzione sia denominata **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva alla soluzione.

3. Nella finestra di dialogo **Nuova app multipiattaforma** fare clic su **App vuota**, selezionare **.NET Standard** come strategia di condivisione del codice e fare clic sul pulsante **OK**:

    ![](single-page-images/vs/new-app.png "New Cross-Platform App")

    Per ulteriori informazioni sulla libreria di .NET Standard creata, vedere la pagina relativa all' [anatomia di un' Xamarin.Forms applicazione](deepdive.md#anatomy-of-a-xamarinforms-application) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

4. Fare doppio clic su **MainPage.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file:

    ![](single-page-images/vs/open-mainpage-xaml.png "Open MainPage.xaml")

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da un oggetto [`Label`](xref:Xamarin.Forms.Label) per visualizzare il testo, un oggetto [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e due [`Button`](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due `Button` istanze sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid) , con l'oggetto, `Label` `Editor` e `Grid` vengono disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Per ulteriori informazioni sulla creazione dell'interfaccia utente, vedere [interfaccia utente](deepdive.md#user-interface) nell' [ Xamarin.Forms esercitazione approfondita di avvio rapido](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml** premendo **CTRL+S** e chiudere il file.

6. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **Notes** e fare doppio clic su **MainPage.xaml.cs** per aprirlo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Open MainPage.xaml.cs")

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

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se esistente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor) . Quando **Save** [`Button`](xref:Xamarin.Forms.Button) viene premuto il pulsante Salva `OnSaveButtonClicked` , viene eseguito il gestore eventi, il quale salva il contenuto di nel `Editor` file. Se per selezionare **Elimina si preme ** `Button`, viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`. Per ulteriori informazioni sull'interazione dell'utente, vedere [risposta all'interazione dell'utente](deepdive.md#responding-to-user-interaction) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio selezionare la voce di menu **Compila > Compila soluzione** o premere F6. La soluzione viene compilata e nella barra di stato di Visual Studio viene visualizzato un messaggio di operazione completata:

      ![](single-page-images/vs/build-succeeded.png "Build Succeeded")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori fino a quando la soluzione non viene compilata correttamente.

2. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione in un emulatore Android:

    ![](single-page-images/vs/android-start.png "Visual Studio Android Toolbar")

    [![](single-page-images/vs/notes-android.png "Notes in the Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni su come viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

    > [!NOTE]
    > La procedura seguente deve essere eseguita solo se si dispone di un [Mac associato](~/ios/get-started/installation/windows/connecting-to-mac/index.md) che soddisfa i requisiti di sistema per Xamarin.Forms lo sviluppo.

3. Nella barra degli strumenti di Visual Studio fare clic con il pulsante destro del mouse sul progetto **Notes.iOS** e selezionare **Imposta come progetto di avvio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Set iOS as Startup Project")

4. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione nel [simulatore iOS remoto](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Visual Studio iOS Toolbar")

    [![](single-page-images/vs/notes-ios.png "Notes in the iOS Simulator")](single-page-images/vs/notes-ios-large.png#lightbox "Notes in the iOS Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni su come viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Prerequisiti

- Visual Studio per Mac (versione più recente) con installato il supporto delle piattaforme Android e iOS.
- Xcode (versione più recente).
- Conoscenza di C#.

Per altre informazioni su questi prerequisiti, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

1. Avviare Visual Studio per Mac e nella finestra iniziale fare clic su **Nuovo** per creare un nuovo progetto:

    ![](single-page-images/vsmac/new-project.png "New Solution")

2. Nella finestra di dialogo, **Scegli un modello per il nuovo progetto** fare clic su **Multipiattaforma > App**, selezionare il modello **App Forms vuota** e fare clic sul pulsante **Avanti**:

    ![](single-page-images/vsmac/choose-template.png "Choose a Template")

3. Nella finestra di dialogo **Configura l'app Forms vuota** denominare la nuova app **Notes**, verificare che sia selezionato il pulsante di opzione **Usa .NET Standard** e fare clic sul pulsante **Avanti**:    

    ![](single-page-images/vsmac/configure-app.png "Configure the Forms Application")

4. Nella finestra di dialogo **Configura l'app Forms vuota** lasciare i nomi di soluzione e progetto impostati su **Notes**, scegliere un percorso appropriato per il progetto e fare clic sul pulsante **Crea** per creare il progetto:

    ![](single-page-images/vsmac/configure-project.png "Configure the Forms Project")

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa guida introduttiva richiedono che sia la soluzione che il progetto siano denominati **Notes**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa guida introduttiva al progetto.

    Per ulteriori informazioni sulla libreria di .NET Standard creata, vedere la pagina relativa all' [anatomia di un' Xamarin.Forms applicazione](deepdive.md#anatomy-of-a-xamarinforms-application) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina, che è costituita da un oggetto [`Label`](xref:Xamarin.Forms.Label) per visualizzare il testo, un oggetto [`Editor`](xref:Xamarin.Forms.Editor) per l'input di testo e due [`Button`](xref:Xamarin.Forms.Button) istanze che indirizzano l'applicazione al salvataggio o all'eliminazione di un file. Le due `Button` istanze sono disposte orizzontalmente in un oggetto [`Grid`](xref:Xamarin.Forms.Grid) , con l'oggetto, `Label` `Editor` e `Grid` vengono disposti verticalmente in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Per ulteriori informazioni sulla creazione dell'interfaccia utente, vedere [interfaccia utente](deepdive.md#user-interface) nell' [ Xamarin.Forms esercitazione approfondita di avvio rapido](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

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

    Questo codice definisce un campo `_fileName`, che fa riferimento a un file denominato `notes.txt` che archivia i dati delle note nella cartella locale dei dati delle applicazioni per l'applicazione. Quando viene eseguito il costruttore della pagina, il file viene letto, se esistente, e visualizzato in [`Editor`](xref:Xamarin.Forms.Editor) . Quando **Save** [`Button`](xref:Xamarin.Forms.Button) viene premuto il pulsante Salva `OnSaveButtonClicked` , viene eseguito il gestore eventi, il quale salva il contenuto di nel `Editor` file. Se per selezionare **Elimina si preme ** `Button`, viene eseguito il gestore dell'evento `OnDeleteButtonClicked`, che elimina il file, a condizione che esista, e rimuove qualsiasi testo da `Editor`. Per ulteriori informazioni sull'interazione dell'utente, vedere [risposta all'interazione dell'utente](deepdive.md#responding-to-user-interaction) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

    Salvare le modifiche apportate a **MainPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S**, e chiudere il file.

### <a name="building-the-quickstart"></a>Compilazione della guida rapida

1. In Visual Studio per Mac, selezionare la voce di menu **Compila > Compila tutto** o premere **&#8984; + B**. I progetti vengono compilati e viene visualizzato un messaggio di operazione completata nella barra degli strumenti di Visual Studio per Mac.

      ![](single-page-images/vsmac/build-successful.png "Build Successful")

    In caso di errori, ripetere i passaggi precedenti e correggere gli errori finché i progetti non vengono compilati correttamente.

2. Nel **riquadro della soluzione** selezionare il progetto **Notes.iOS**, fare clic con il pulsante destro del mouse e scegliere **Imposta come progetto di avvio**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Set iOS as Startup Project")

3. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS prescelto:

      ![](single-page-images/vsmac/start.png "Visual Studio for Mac Toolbar")

      [![](single-page-images/vsmac/notes-ios.png "Notes in the iOS Simulator")](single-page-images/vsmac/notes-ios-large.png#lightbox "Notes in the iOS Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni su come viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

4. Nel **riquadro della soluzione** selezionare il progetto **Notes.Droid**, fare clic con il pulsante destro del mouse e scegliere **Imposta come progetto di avvio**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Set Android as Startup Project")

5. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno dell'emulatore Android prescelto:

      [![](single-page-images/vsmac/notes-android.png "Notes in the Android Emulator")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Immettere una nota e premere il pulsante **Salva**.

    Per altre informazioni su come viene avviata l'applicazione in ogni piattaforma, vedere [avvio dell'applicazione in ogni piattaforma](deepdive.md#launching-the-application-on-each-platform) nella [ Xamarin.Forms Guida introduttiva](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

- Creare un'applicazione multipiattaforma Xamarin.Forms .
- Definire l'interfaccia utente per una pagina usando eXtensible Application Markup Language (XAML).
- Interagire con gli elementi dell'interfaccia utente XAML dal codice.

Per trasformare questa applicazione a singola pagina in un'applicazione multipagina, passare all'argomento di avvio rapido successivo.

> [!div class="nextstepaction"]
> [Avanti](multi-page.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Xamarin.FormsApprofondimento sulla Guida introduttiva](deepdive.md)
