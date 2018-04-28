---
title: Programma di installazione WPF della piattaforma
description: Xamarin. Forms include ora il supporto di anteprima per la piattaforma di WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 2e2bbf12cd7b4abab4609349b549fde1bcea09e8
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="wpf-platform-setup"></a>Programma di installazione WPF della piattaforma

![Anteprima](~/media/shared/preview.png)

Xamarin. Forms dispone ora di anteprima il supporto per Windows Presentation Foundation (WPF). In questo articolo viene illustrato come aggiungere un progetto WPF a una soluzione xamarin. Forms.

Prima di iniziare, creare una nuova soluzione xamarin. Forms in Visual Studio 2017 oppure usare una soluzione xamarin. Forms esistente, ad esempio, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). È possibile aggiungere solo app WPF a una soluzione xamarin. Forms in Windows.

## <a name="adding-a-wpf-app"></a>Aggiunta di un'applicazione WPF

Seguire queste istruzioni per aggiungere un'applicazione WPF che verrà eseguita nel desktop di Windows 7, 8 e 10:

1. In Visual Studio 2017, fare clic sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi > Nuovo progetto...** .

2. Nel **nuovo progetto** finestra, l'istruzione select sinistro **Visual c#** e **Windows Desktop classico**. Nell'elenco dei tipi di progetto, scegliere **applicazione WPF (.NET Framework)**. 

3. Digitare un nome per il progetto con una **WPF** estensione, ad esempio **BoxViewClock.WPF**. Fare clic sul **esplorare** pulsante, seleziona il **BoxViewClock** cartella, quindi premere **Seleziona cartella**. Il progetto WPF questo viene inserito nella stessa directory in altri progetti nella soluzione.

    ![Aggiungere un nuovo progetto WPF](wpf-images/add-new-project.png "aggiungere un nuovo progetto WPF")

    Fare clic su OK per creare il progetto.

4. Nel **Esplora soluzioni**, a destra fare clic su nuovo **BoxViewClock.WPF** del progetto e selezionare **Gestisci pacchetti NuGet**. Selezionare il **esplorare** scheda, fare clic sul **Includi versione preliminare** casella di controllo per cercare **xamarin. Forms**.

    ![Selezionare il pacchetto NuGet](wpf-images/select-nuget-package.png "selezionare il pacchetto NuGet")

    Selezionare pacchetto e fare clic sui **installare** pulsante.

5. La ricerca **Xamarin.Forms.Platform.WPF** comprimere e installare quello anche. Assicurarsi che il pacchetto proviene da Microsoft.

6. Pulsante destro del mouse sul nome della soluzione nella **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet per la soluzione**. Selezionare il **aggiornamento** scheda e il **xamarin. Forms** pacchetto. Selezionare tutti i progetti e aggiornarli alla stessa versione di xamarin. Forms:

    ![Aggiornare il pacchetto NuGet](wpf-images/update-nuget-package.png "aggiornare il pacchetto NuGet") 

7. Nel progetto WPF, fare clic su **riferimenti**. Nel **gestione riferimenti** finestra di dialogo, seleziona **progetti** a sinistra e controllare la casella di controllo accanto al **BoxViewClock** progetto:

    ![Fare riferimento a progetto condiviso](wpf-images/reference-shared-project.png "fa riferimento al progetto condiviso")

8. Modificare il **MainWindow. XAML** file del progetto WPF. Nel `Window` tag, aggiungere una dichiarazione dello spazio dei nomi XML per il **Xamarin.Forms.Platform.WPF** assembly e lo spazio dei nomi:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Modificare ora la `Window` tag a `wpf:FormsApplicationPage`. Modifica il `Title` impostare il nome dell'applicazione, ad esempio, **BoxViewClock**. Il file XAML completato dovrebbe essere simile al seguente:

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

9. Modificare il **MainWindow.xaml.cs** file del progetto WPF. Aggiungere due nuove `using` direttive:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Modificare la classe di base `MainWindow` da `Window` a `FormsApplicationPage`. Seguendo il `InitializeComponent` chiamare, aggiungere le due istruzioni seguenti:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Ad eccezione di commenti e inutilizzato `using` direttive, l'intero **il file MainWindows.xaml.cs** file dovrebbe essere simile al seguente:

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

10. Fare clic sul progetto WPF nel **Esplora soluzioni** e selezionare **imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![WPF BoxView Clock](wpf-images/wpf-boxviewclock.png "Clock BoxView WPF" )

## <a name="next-steps"></a>Passaggi successivi

### <a name="platform-specifics"></a>Specifiche della piattaforma

È possibile determinare quale piattaforma dell'applicazione di xamarin. Forms è in esecuzione dal codice o XAML. In questo modo è possibile modificare le caratteristiche di programma mentre è in esecuzione su WPF. Nel codice, confrontare il valore di `Device.RuntimePlatform` con il `Device.WPF` costante (che è uguale alla stringa "WPF"). Se non esiste una corrispondenza, l'applicazione è in esecuzione su WPF.

In XAML, è possibile utilizzare il `OnPlatform` tag per selezionare un valore di proprietà specifico per la piattaforma:

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

È possibile regolare le dimensioni iniziali della finestra in WPF **MainWindow. XAML** file:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, pertanto è opportuno prevedere che non siano pronti di produzione. Non tutti i pacchetti NuGet per xamarin. Forms sono pronti per WPF e alcune funzionalità potrebbero non essere completamente funzionante.

