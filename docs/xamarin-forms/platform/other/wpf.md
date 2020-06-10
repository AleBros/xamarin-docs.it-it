---
title: "configurazione della piattaforma WPF" Descrizione: " Xamarin.Forms ora dispone del supporto per l'anteprima della piattaforma WPF" ms. prod: Novell MS. AssetID: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E ms. Technology: Novell-Forms ms. Custom: Xamu-video Author: davidbritch ms. Author: dabritch ms. Date: 04/09/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="wpf-platform-setup"></a>Configurazione della piattaforma WPF

![Anteprima](~/media/shared/preview.png)

Xamarin.Formsdispone ora del supporto in anteprima per il Windows Presentation Foundation (WPF). Questo articolo illustra come aggiungere un progetto WPF a una Xamarin.Forms soluzione.

> [!IMPORTANT]
> Xamarin.Formsil supporto per WPF viene fornito dalla community. Per ulteriori informazioni, vedere [ Xamarin.Forms supporto della piattaforma](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Prima di iniziare, creare una nuova Xamarin.Forms soluzione in Visual Studio 2019 o usare una soluzione esistente Xamarin.Forms , ad esempio [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). È possibile aggiungere app WPF solo a una Xamarin.Forms soluzione di Windows.

## <a name="add-a-wpf-application"></a>Aggiungere un'applicazione WPF

Seguire queste istruzioni per aggiungere un'applicazione WPF che viene eseguita su Windows 7, 8 e 10 desktop:

1. In Visual Studio 2019, fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi > nuovo progetto.**

2. Nella finestra **Aggiungi nuovo progetto** selezionare **C#** nell'elenco a discesa **lingue** , selezionare **Windows** nell'elenco a discesa **piattaforme** e selezionare **Desktop** nell'elenco a discesa tipo di **progetto** . Nell'elenco dei tipi di progetto scegliere **app WPF (.NET Framework)**:

    ![Aggiungere un nuovo progetto WPF](wpf-images/add-project.png "Aggiungere un nuovo progetto WPF")

    Premere il pulsante **Avanti** .

3. Nella finestra **Configura nuovo progetto** Digitare un nome per il progetto con un'estensione **WPF** , ad esempio **BoxViewClock. WPF**. Fare clic sul pulsante **Sfoglia** , selezionare la cartella **BoxViewClock** e quindi fare clic su **Seleziona cartella** per inserire il progetto WPF nella stessa directory degli altri progetti nella soluzione:

    ![Aggiungere un nuovo progetto WPF](wpf-images/configure-project.png "Aggiungere un nuovo progetto WPF")

    Premere il pulsante **Crea** per creare il progetto.

4. Nel **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nuovo progetto **BoxViewClock. WPF** e scegliere **Gestisci pacchetti NuGet...**. Selezionare la scheda **Sfoglia** e cercare ** Xamarin.Forms . Platform. WPF**:

    ![Selezionare il pacchetto NuGet](wpf-images/select-nuget-package.png "Selezionare il pacchetto NuGet")

    Selezionare il pacchetto e fare clic sul pulsante **Installa** .

5. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet per la soluzione...**. Selezionare la scheda **aggiornamenti** , quindi selezionare il **Xamarin.Forms** pacchetto. Selezionare tutti i progetti e aggiornarli alla stessa Xamarin.Forms versione:

    ![Aggiornare il pacchetto NuGet](wpf-images/update-nuget-package.png "Aggiornare il pacchetto NuGet")

6. Nel progetto WPF, fare clic con il pulsante destro del mouse su **riferimenti** e selezionare **Aggiungi riferimento.** Nella finestra di dialogo **Gestione riferimenti** selezionare **progetti** a sinistra e selezionare la casella di controllo accanto al progetto **BoxViewClock** :

    ![Fare riferimento al progetto condiviso](wpf-images/reference-shared-project.png "Fare riferimento al progetto condiviso")

    Premere il pulsante **OK** .

7. Modificare il file **MainWindow. XAML** del progetto WPF. Nel `Window` tag aggiungere una dichiarazione dello spazio dei nomi XML per l'oggetto ** Xamarin.Forms . Assembly Platform. WPF** e spazio dei nomi:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    A questo punto `Window` , modificare il tag in `wpf:FormsApplicationPage` . Modificare l' `Title` impostazione impostando il nome dell'applicazione, ad esempio **BoxViewClock**. Il file XAML completato dovrebbe essere simile al seguente:

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

8. Modificare il file **MainWindow.XAML.cs** del progetto WPF. Aggiungere due nuove `using` direttive:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Modificare la classe di base di `MainWindow` da `Window` a `FormsApplicationPage` . Dopo la `InitializeComponent` chiamata, aggiungere le due istruzioni seguenti:

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

9. Fare clic con il pulsante destro del mouse sul progetto WPF nel **Esplora soluzioni** e selezionare **Imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Clock BoxView WPF](wpf-images/wpf-boxviewclock.png "Clock BoxView WPF" )

## <a name="platform-specifics"></a>Specifiche della piattaforma

È possibile determinare la piattaforma in cui Xamarin.Forms viene eseguita l'applicazione dal codice o da XAML. In questo modo è possibile modificare le caratteristiche del programma quando viene eseguito in WPF. Nel codice confrontare il valore di `Device.RuntimePlatform` con la `Device.WPF` costante (che corrisponde alla stringa "WPF"). Se esiste una corrispondenza, l'applicazione è in esecuzione in WPF.

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

## <a name="window-size"></a>Dimensioni finestra

È possibile modificare le dimensioni iniziali della finestra nel file **MainWindow. XAML** di WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, quindi è necessario aspettarsi che non tutti gli elementi siano pronti per la produzione. Non tutti i pacchetti NuGet per Xamarin.Forms sono pronti per WPF e alcune funzionalità potrebbero non funzionare in modo completo.

## <a name="related-video"></a>Video correlato

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms3,0 video del supporto WPF**
