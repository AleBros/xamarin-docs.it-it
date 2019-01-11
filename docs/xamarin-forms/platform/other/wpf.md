---
title: Programma di installazione piattaforma WPF
description: Xamarin. Forms include ora il supporto di anteprima per la piattaforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: cdf115c4ea6d6613a1da2d0d2cfa14ed500086f8
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209186"
---
# <a name="wpf-platform-setup"></a>Programma di installazione piattaforma WPF

![Anteprima](~/media/shared/preview.png)

Xamarin. Forms dispone ora di supporto in anteprima per Windows Presentation Foundation (WPF). Questo articolo illustra come aggiungere un progetto WPF in una soluzione xamarin. Forms.

Prima di iniziare, creare una nuova soluzione xamarin. Forms in Visual Studio 2017 o usare una soluzione xamarin. Forms esistente, ad esempio, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). È possibile aggiungere solo le app WPF a una soluzione xamarin. Forms in Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Aggiungere un progetto WPF in un'app xamarin. Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin. Forms 3.0 supporto WPF, da [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>Aggiunta di un'App WPF

Seguire queste istruzioni per aggiungere un'app WPF che verrà eseguita sui desktop Windows 7, 8 e 10:

1. In Visual Studio 2017, fare doppio clic sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi > Nuovo progetto...** .

2. Nel **nuovo progetto** finestra, l'istruzione select a sinistra **Visual c#** e **Desktop classico di Windows**. Nell'elenco dei tipi di progetto, scegliere **App WPF (.NET Framework)**. 

3. Digitare un nome per il progetto con un **WPF** estensione, ad esempio **BoxViewClock.WPF**. Fare clic sul **esplorare** pulsante, seleziona il **BoxViewClock** cartella, quindi premere **selezionare la cartella**. Il progetto WPF verrà impostato nella stessa directory in altri progetti nella soluzione.

    ![Aggiungere un nuovo progetto WPF](wpf-images/add-new-project.png "aggiungere un nuovo progetto WPF")

    Fare clic su OK per creare il progetto.

4. Nel **Esplora soluzioni**, a destra fare clic su nuovo **BoxViewClock.WPF** del progetto e selezionare **Gestisci pacchetti NuGet**. Selezionare il **esplorare** scheda, fare clic sui **Includi versione preliminare** casella di controllo e cercare **xamarin. Forms**.

    ![Selezionare il pacchetto NuGet](wpf-images/select-nuget-package.png "selezionare il pacchetto NuGet")

    Selezione del pacchetto e fare clic sui **installare** pulsante.

5. Ora cercare **Xamarin.Forms.Platform.WPF** pacchetti e la installa anche che uno. Assicurarsi che il pacchetto proviene da Microsoft.

6. Pulsante destro del mouse sul nome della soluzione nella **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet per la soluzione**. Selezionare il **Update** scheda e il **xamarin. Forms** pacchetto. Selezionare tutti i progetti e aggiornarli per la stessa versione di xamarin. Forms:

    ![Aggiornare il pacchetto NuGet](wpf-images/update-nuget-package.png "aggiornare il pacchetto NuGet") 

7. Nel progetto WPF, fare clic su **riferimenti**. Nel **gestione riferimenti** finestra di dialogo, seleziona **progetti** a sinistra, quindi la casella di controllo accanto a controllo la **BoxViewClock** progetto:

    ![Fare riferimento al progetto condiviso](wpf-images/reference-shared-project.png "fanno riferimento a progetto condiviso")

8. Modificare il **MainWindow. XAML** file del progetto WPF. Nel `Window` tag, aggiungere una dichiarazione dello spazio dei nomi XML per il **Xamarin.Forms.Platform.WPF** assembly e dello spazio dei nomi:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Modificare ora la `Window` tag di `wpf:FormsApplicationPage`. Modifica il `Title` impostando il nome dell'applicazione, ad esempio, **BoxViewClock**. Il file XAML completato dovrebbe essere simile al seguente:

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

    Modificare la classe di base `MainWindow` dal `Window` a `FormsApplicationPage`. Seguendo il `InitializeComponent` chiamare, aggiungere le due istruzioni seguenti:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Fatta eccezione per i commenti e inutilizzato `using` direttive, l'intero **MainWindows.xaml.cs** file dovrebbe essere simile al seguente:

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

10. Fare doppio clic su progetto WPF nel **Esplora soluzioni** e selezionare **imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Oggetto Clock WPF BoxView](wpf-images/wpf-boxviewclock.png "BoxView oggetto Clock WPF" )

## <a name="next-steps"></a>Passaggi successivi

### <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

È possibile determinare in quale piattaforma l'applicazione xamarin. Forms è in esecuzione dal codice o XAML. In questo modo è possibile modificare le caratteristiche di programma in esecuzione su WPF. Nel codice, confrontare il valore della `Device.RuntimePlatform` con il `Device.WPF` costante (che è uguale alla stringa "WPF"). Se non esiste una corrispondenza, l'applicazione è in esecuzione su WPF.

In XAML, è possibile usare il `OnPlatform` tag per selezionare un valore di proprietà specifico della piattaforma:

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

È possibile regolare le dimensioni iniziali della finestra di WPF **MainWindow. XAML** file:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, pertanto è opportuno prevedere che non siano pronte per la produzione. Non tutti i pacchetti NuGet per xamarin. Forms sono pronti per WPF e alcune funzionalità potrebbero non essere completamente funzionante.

