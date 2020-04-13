---
title: Installazione della piattaforma WPFWPF Platform Setup
description: Xamarin.Forms dispone ora del supporto di anteprima per la piattaforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: c9ec9fec2391d7d7a24f97f2ec20208a7d69dbc1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992334"
---
# <a name="wpf-platform-setup"></a>Configurazione della piattaforma WPFWPF platform setup

![Anteprima](~/media/shared/preview.png)

Xamarin.Forms dispone ora del supporto di anteprima per Windows Presentation Foundation (WPF). In questo articolo viene illustrato come aggiungere un progetto WPF a una soluzione Xamarin.Forms.This article demonstrates how to add a WPF project to a Xamarin.Forms solution.

> [!IMPORTANT]
> Il supporto Xamarin.Forms per WPF viene fornito dalla community. Per ulteriori informazioni, vedere [Supporto della piattaforma Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Prima di iniziare, creare una nuova soluzione Xamarin.Forms in Visual Studio 2019 oppure utilizzare una soluzione Xamarin.Forms esistente, ad esempio [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). È possibile aggiungere solo app WPF a una soluzione Xamarin.Forms in Windows.You can only add WPF apps to a Xamarin.Forms solution in Windows.

## <a name="add-a-wpf-application"></a>Aggiungere un'applicazione WPFAdd a WPF application

Seguire queste istruzioni per aggiungere un'applicazione WPF che verrà eseguita nei desktop Windows 7, 8 e 10:Follow these instructions to add a WPF application that will run on the Windows 7, 8, and 10 desktops:

1. In Visual Studio 2019, fare clic con il pulsante destro del mouse sul nome della soluzione in **Esplora soluzioni** e scegliere **Aggiungi > nuovo progetto...**.

2. Nel **Aggiungi un nuovo progetto** finestra, selezionare **C ,** nel **lingue** menu a discesa, selezionare **Windows** nel **Piattaforme** menu 'A tendina e selezionare **Desktop** nel tipo di **progetto** menu 'A tendina. Nell'elenco dei tipi di progetto scegliere **App WPF (.NET Framework)**:

    ![Aggiungere un nuovo progetto WPFAdd a new WPF project](wpf-images/add-project.png "Aggiungere un nuovo progetto WPFAdd a new WPF project")

    Premere il pulsante **Avanti.**

3. Nella finestra **Configura il nuovo progetto digitare** un nome per il progetto con un'estensione **WPF,** ad esempio **BoxViewClock.WPF**. Fare clic sul pulsante **Sfoglia,** selezionare la cartella **BoxViewClock** e premere **Seleziona cartella** per inserire il progetto WPF nella stessa directory degli altri progetti nella soluzione:

    ![Aggiungere un nuovo progetto WPFAdd a new WPF project](wpf-images/configure-project.png "Aggiungere un nuovo progetto WPFAdd a new WPF project")

    Premere il pulsante **Crea** per creare il progetto.

4. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nuovo progetto **BoxViewClock.WPF** e scegliere **Gestisci pacchetti NuGet...**. Selezionare la scheda **Sfoglia** e cercare **Xamarin.Forms.Platform.WPF**:

    ![Selezionare il pacchetto NuGet](wpf-images/select-nuget-package.png "Selezionare il pacchetto NuGet")

    Selezionare il pacchetto e fare clic sul pulsante **Installa.**

5. Fare clic con il pulsante destro del mouse sul nome della soluzione in **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet per soluzione...**. Selezionare la scheda **Aggiornamenti,** quindi selezionare il pacchetto **Xamarin.Forms.** Selezionare tutti i progetti e aggiornarli alla stessa versione Xamarin.Forms:

    ![Aggiornare il pacchetto NuGetUpdate the NuGet package](wpf-images/update-nuget-package.png "Aggiornare il pacchetto NuGetUpdate the NuGet package")

6. Nel progetto WPF, fare clic con il pulsante destro del mouse su **Riferimenti** e selezionare **Aggiungi riferimento...**. Nella finestra di dialogo **Gestione riferimenti,** selezionare **Progetti** a sinistra e selezionare la casella di controllo adiacente al progetto **BoxViewClock:**

    ![Riferimento al progetto condiviso](wpf-images/reference-shared-project.png "Riferimento al progetto condiviso")

    Premere il pulsante **OK.**

7. Modificare il file **MainWindow.xaml** del progetto WPF. Nel `Window` tag aggiungere una dichiarazione dello spazio dei nomi XML per l'assembly e lo spazio dei **nomi Xamarin.Forms.Platform.WPF:**

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Modificare ora `Window` il `wpf:FormsApplicationPage`tag in . Modificare `Title` l'impostazione con il nome dell'applicazione, ad esempio **BoxViewClock**. Il file XAML completato dovrebbe essere simile al seguente:The completed XAML file should look like this:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"            
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

8. Modificare il **file MainWindow.xaml.cs** del progetto WPF. Aggiungere due `using` nuove direttive:Add two new directives:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Modificare la classe `MainWindow` `Window` base `FormsApplicationPage`da da a . Dopo `InitializeComponent` la chiamata, aggiungere le due istruzioni seguenti:Following the call, add the following two statements:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    Ad eccezione dei `using` commenti e delle direttive inutilizzate, il file **di MainWindows.xaml.cs** completo dovrebbe essere simile al seguente:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

9. Fare clic con il pulsante destro del mouse sul progetto WPF in **Esplora soluzioni** e selezionare Imposta come progetto di **avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Orologio BOXView WPF](wpf-images/wpf-boxviewclock.png "Orologio BOXView WPF" )

## <a name="platform-specifics"></a>Specifiche della piattaforma

È possibile determinare su quale piattaforma è in esecuzione l'applicazione Xamarin.Forms dal codice o da XAML. In questo modo è possibile modificare le caratteristiche del programma quando è in esecuzione in WPFWPF. Nel codice confrontare `Device.RuntimePlatform` il `Device.WPF` valore di con la costante (che è uguale alla stringa "WPF"). Se esiste una corrispondenza, l'applicazione è in esecuzione in WPFWPF.

In XAML puoi usare `OnPlatform` il tag per selezionare un valore di proprietà specifico per la piattaforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

## <a name="window-size"></a>Dimensioni finestra

È possibile regolare le dimensioni iniziali della finestra nel file **WPF MainWindow.xaml:**

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemi

Questa è un'anteprima, quindi dovresti aspettarti che non tutto sia pronto per la produzione. Non tutti i pacchetti NuGet per Xamarin.Forms sono pronti per WPFWPF e alcune funzionalità potrebbero non funzionare completamente.

## <a name="related-video"></a>Video correlato

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 Video di supporto WPF**
