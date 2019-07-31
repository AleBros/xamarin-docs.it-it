---
title: Configurazione della piattaforma WPF
description: Novell. Forms ora dispone del supporto in anteprima per la piattaforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 35652c0fc0e64686a79b2ea0fe96a1fca0b2825d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652842"
---
# <a name="wpf-platform-setup"></a>Configurazione della piattaforma WPF

![Anteprima](~/media/shared/preview.png)

In Novell. Forms è ora disponibile il supporto in anteprima per il Windows Presentation Foundation (WPF). Questo articolo illustra come aggiungere un progetto WPF a una soluzione Novell. Forms.

Prima di iniziare, creare una nuova soluzione Novell. Forms in Visual Studio 2019 oppure usare una soluzione Novell. Forms esistente, ad esempio [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). È possibile aggiungere app WPF solo a una soluzione Novell. Forms in Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Aggiungere un progetto WPF a un'app Novell. Forms con Novell. University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Video di supporto WPF per Novell. Forms 3,0**

## <a name="adding-a-wpf-app"></a>Aggiunta di un'app WPF

Seguire queste istruzioni per aggiungere un'app WPF che viene eseguita su Windows 7, 8 e 10 desktop:

1. In Visual Studio 2019, fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi > nuovo progetto.**

2. Nella finestra **nuovo progetto** , a sinistra, selezionare **Visual C#**  e **desktop classico di Windows**. Nell'elenco dei tipi di progetto scegliere **app WPF (.NET Framework)** . 

3. Digitare un nome per il progetto con un'estensione **WPF** , ad esempio **BoxViewClock. WPF**. Fare clic sul pulsante **Sfoglia** , selezionare la cartella **BoxViewClock** e quindi **selezionare cartella**. Il progetto WPF verrà inserito nella stessa directory degli altri progetti della soluzione.

    ![Aggiungere un nuovo progetto WPF](wpf-images/add-new-project.png "Aggiungere un nuovo progetto WPF")

    Fare clic su OK per creare il progetto.

4. Nel **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nuovo progetto **BoxViewClock. WPF** e scegliere **Gestisci pacchetti NuGet**. Selezionare la scheda **Sfoglia** , fare clic sulla casella di controllo **Includi versione preliminare** e cercare **Novell. Forms**.

    ![Selezionare il pacchetto NuGet](wpf-images/select-nuget-package.png "Selezionare il pacchetto NuGet")

    Selezionare il pacchetto e fare clic sul pulsante **Installa** .

5. A questo punto, cercare il pacchetto **Novell. Forms. Platform. WPF** e installarne anche uno. Verificare che il pacchetto provenga da Microsoft.

6. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet per la soluzione**. Selezionare la scheda **aggiornamento** e il pacchetto **Novell. Forms** . Selezionare tutti i progetti e aggiornarli alla stessa versione di Novell. Forms:

    ![Aggiornare il pacchetto NuGet](wpf-images/update-nuget-package.png "Aggiornare il pacchetto NuGet") 

7. Nel progetto WPF, fare clic con il pulsante destro del mouse su **riferimenti**. Nella finestra di dialogo **Gestione riferimenti** selezionare **progetti** a sinistra e selezionare la casella di controllo accanto al progetto **BoxViewClock** :

    ![Fare riferimento al progetto condiviso](wpf-images/reference-shared-project.png "Fare riferimento al progetto condiviso")

8. Modificare il file **MainWindow. XAML** del progetto WPF. Nel tag aggiungere una dichiarazione dello spazio dei nomi XML per l'assembly **Novell. Forms. Platform. WPF** e lo spazio dei nomi: `Window`

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    A questo punto `Window` , modificare `wpf:FormsApplicationPage`il tag in. Modificare l' `Title` impostazione impostando il nome dell'applicazione, ad esempio **BoxViewClock**. Il file XAML completato dovrebbe essere simile al seguente:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. Modificare il file **MainWindow.XAML.cs** del progetto WPF. Aggiungere due nuove `using` direttive:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Modificare la classe di base `MainWindow` di `Window` da `FormsApplicationPage`a. Dopo la `InitializeComponent` chiamata, aggiungere le due istruzioni seguenti:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Fatta eccezione per i commenti e `using` le direttive inutilizzate, il file **MainWindows.XAML.cs** completo dovrebbe essere simile al seguente:

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

10. Fare clic con il pulsante destro del mouse sul progetto WPF nel **Esplora soluzioni** e selezionare **Imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Clock BoxView WPF] (wpf-images/wpf-boxviewclock.png "Clock BoxView WPF" )

## <a name="next-steps"></a>Passaggi successivi

### <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

È possibile determinare la piattaforma in cui è in esecuzione l'applicazione Novell. Forms da codice o XAML. In questo modo è possibile modificare le caratteristiche del programma quando viene eseguito in WPF. Nel codice confrontare il valore di `Device.RuntimePlatform` con la `Device.WPF` costante (che corrisponde alla stringa "WPF"). Se esiste una corrispondenza, l'applicazione è in esecuzione in WPF.

In XAML è possibile usare il `OnPlatform` tag per selezionare un valore della proprietà specifico per la piattaforma:

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

### <a name="window-size"></a>Dimensioni finestra

È possibile modificare le dimensioni iniziali della finestra nel file **MainWindow. XAML** di WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, quindi è necessario aspettarsi che non tutti gli elementi siano pronti per la produzione. Non tutti i pacchetti NuGet per Novell. Forms sono pronti per WPF e alcune funzionalità potrebbero non funzionare completamente.

