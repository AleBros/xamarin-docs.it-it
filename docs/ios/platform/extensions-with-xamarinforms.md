---
title: Riutilizzare le pagine di Novell. Forms in un'estensione iOS
description: Questo documento descrive le estensioni iOS e come usare le pagine di Novell. Forms. Contiene una procedura dettagliata per la creazione di un'estensione, l'integrazione di Novell. Forms e il rendering di un semplice ContentPage in modo nativo in un progetto di estensione iOS.
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: 4006bb3ef82264b5a7a6d764719bee81a8ce78a1
ms.sourcegitcommit: 5bc37b384706bfbf5bf8e5b1288a34ddd0f3303b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/15/2020
ms.locfileid: "83418014"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>Riutilizzare le pagine di Novell. Forms in un'estensione iOS

le estensioni iOS consentono di personalizzare il comportamento del sistema esistente aggiungendo funzionalità aggiuntive a [predefinite da punti di estensione iOS e MacOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2), ad esempio azioni di contesto personalizzate, riempimento automatico delle password, filtri delle chiamate in ingresso, modificatori del contenuto delle notifiche e altro ancora. Novell. iOS supporta le estensioni e [questa guida](https://docs.microsoft.com/xamarin/ios/platform/extensions) illustra in modo dettagliato la creazione di un'estensione iOS usando gli strumenti di Novell.

Le estensioni vengono distribuite come parte di un'app contenitore e attivate da uno specifico punto di estensione in un'app host. L'app contenitore è in genere una semplice applicazione iOS, che fornisce a un utente informazioni sull'estensione, su come attivarla e usarla. Esistono tre approcci principali per la condivisione del codice tra un'estensione e un'app contenitore:

1. Progetto iOS comune.

    È possibile inserire tutto il codice condiviso tra il contenitore e l'estensione in una libreria iOS condivisa e fare riferimento alla libreria da entrambi i progetti. In genere, la libreria condivisa contiene UIViewControllers nativi e deve essere una libreria Novell. iOS.

1. Collegamenti al file.

    In alcuni casi, l'app contenitore fornisce la maggior parte delle funzionalità mentre l'estensione deve eseguire il rendering di un singolo `UIViewController` . Con pochi file da condividere, è normale aggiungere un collegamento di file all'app di estensione dal file che si trova nell'app contenitore.

1. Progetto Novell. Forms comune.

    Se le pagine dell'app sono già condivise con un'altra piattaforma, ad esempio Android, usando il Framework Novell. Forms, l'approccio comune consiste nel reimplementare le pagine obbligatorie in modo nativo nel progetto di estensione, perché l'estensione iOS funziona con le pagine UIViewControllers native e non Novell. Forms. È necessario eseguire passaggi aggiuntivi per usare Novell. Forms nell'estensione iOS, illustrati di seguito.

## <a name="xamarinforms-in-an-ios-extension-project"></a>Novell. Forms in un progetto di estensione iOS

La possibilità di utilizzare Novell. Forms in un progetto nativo viene fornita tramite [moduli nativi](~/xamarin-forms/platform/native-forms.md). Consente `ContentPage` di aggiungere pagine derivate da direttamente ai progetti Novell. iOS nativi. Il `CreateViewController` metodo di estensione converte un'istanza di una pagina Novell. Forms in un oggetto nativo `UIViewController` , che può essere usato o modificato come controller normale. Poiché un'estensione iOS è un tipo speciale di progetto iOS nativo, è possibile usare anche i **moduli nativi** .

> [!IMPORTANT]
> Esistono molte [limitazioni note](https://docs.microsoft.com/xamarin/ios/platform/extensions#limitations) per le estensioni iOS. Sebbene sia possibile usare Novell. Forms in un'estensione iOS, è consigliabile eseguire questa operazione con molta attenzione, monitorando l'utilizzo della memoria e i tempi di avvio. In caso contrario, l'estensione potrebbe essere terminata da iOS senza alcun modo per gestirla normalmente.

## <a name="walkthrough"></a>Procedura dettagliata

Questa procedura dettagliata prevede la creazione di un'applicazione Novell. Forms, un'estensione Novell. iOS e il riutilizzo del codice condiviso nel progetto di estensione:

1. Aprire Visual Studio per Mac, creare un nuovo progetto Novell. Forms usando il modello **blank Forms app** e denominarlo **FormsShareExtension**:

    ![Crea progetto](extensions-xf-images/1.walkthrough-createproject.png)

1. In **FormsShareExtension/MainPage. XAML**sostituire il contenuto con il layout seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage
        x:Class="FormsShareExtension.MainPage"
        xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:d="http://xamarin.com/schemas/2014/forms/design"
        xmlns:local="clr-namespace:FormsShareExtension"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        x:DataType="local:MainPageViewModel"
        BackgroundColor="Orange"
        mc:Ignorable="d">
        <ContentPage.BindingContext>
            <local:MainPageViewModel Message="Hello from Xamarin.Forms!" />
        </ContentPage.BindingContext>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <Label
                Margin="20"
                Text="{Binding Message}"
                VerticalOptions="CenterAndExpand" />
            <Button Command="{Binding DoCommand}" Text="Do the job!" />
        </StackLayout>
    </ContentPage>
    ```

1. Aggiungere una nuova classe denominata **MainPageViewMode** al progetto **FormsShareExtension** e sostituire il contenuto della classe con il codice seguente:

    ```csharp
    using System;
    using System.ComponentModel;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace FormsShareExtension
    {
        public class MainPageViewModel : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;

            private string _message;
            public string Message
            {
                get { return _message; }
                set
                {
                    if (_message != value)
                    {
                        _message = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
                    }
                }
            }

            private ICommand _doCommand;
            public ICommand DoCommand
            {
                get { return _doCommand; }
                set
                {
                    if(_doCommand != value)
                    {
                        _doCommand = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(DoCommand)));
                    }
                }
            }

            public MainPageViewModel()
            {
                DoCommand = new Command(OnDoCommandExecuted);
            }

            private void OnDoCommandExecuted(object state)
            {
                Message = $"Job {Environment.TickCount} has been completed!";
            }
        }
    }
    ```

    Il codice è condiviso tra tutte le piattaforme e verrà usato anche da un'estensione iOS.

1. Nel riquadro della soluzione fare clic con il pulsante destro del mouse sulla soluzione, scegliere **aggiungi > nuovo progetto > iOS > estensione > azione estensione**, denominarlo **azione** e fare clic su **Crea**:

    ![Crea estensione](extensions-xf-images/2.walkthrough-createextension.png)

1. Per usare Novell. Forms nell'estensione iOS e il codice condiviso, è necessario aggiungere i riferimenti necessari:

    - Fare clic con il pulsante destro del mouse sull'estensione iOS, scegliere **riferimenti > Aggiungi riferimento > progetti > FormsShareExtension** e premere **OK**.

    - Fare clic con il pulsante destro del mouse sull'estensione iOS, scegliere **pacchetti > Gestisci pacchetti NuGet... > Novell. Forms** e quindi fare clic su **Aggiungi pacchetto**.

1. Espandere il progetto di estensione e modificare un punto di ingresso per inizializzare Novell. Forms e creare pagine. Per i requisiti di iOS, un'estensione deve definire il punto di ingresso in **info. plist** come `NSExtensionMainStoryboard` o `NSExtensionPrincipalClass` . Una volta attivato il punto di ingresso, in questo caso si tratta del `ActionViewController.ViewDidLoad` metodo, è possibile creare un'istanza di una pagina Novell. Forms e visualizzarla a un utente. Quindi, aprire il punto di ingresso e sostituire il `ViewDidLoad` metodo con l'implementazione seguente:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Initialize Xamarin.Forms framework
        global::Xamarin.Forms.Forms.Init();
        // Create an instance of XF page with associated View Model
        var xfPage = new MainPage();
        var viewModel = (MainPageViewModel)xfPage.BindingContext;
        viewModel.Message = "Welcome to XF Page created from an iOS Extension";
        // Override the behavior to complete the execution of the Extension when a user press the button
        viewModel.DoCommand = new Command(() => DoneClicked(this));
        // Convert XF page to a native UIViewController which can be consumed by the iOS Extension
        var newController = xfPage.CreateViewController();
        // Present new view controller as a regular view controller
        this.PresentModalViewController(newController, false);
    }
    ```

    `MainPage`Viene creata un'istanza dell'oggetto usando un costruttore standard e prima di poterlo usare nell'estensione, convertirlo in un oggetto nativo usando `UIViewController` il `CreateViewController` metodo di estensione. 
    
    Compilare ed eseguire l'applicazione:

    ![Crea estensione](extensions-xf-images/3.walkthrough-runapp.png)

    Per attivare l'estensione, passare al browser Safari, digitare un indirizzo Web, ad esempio [Microsoft.com](https://microsoft.com), fare clic su Esplora e quindi premere l'icona **Condividi** nella parte inferiore della pagina per visualizzare le estensioni di azione disponibili. Nell'elenco delle estensioni disponibili selezionare l'estensione per l' **azione** da toccare:

    ![Crea estensione](extensions-xf-images/4.walkthrough-run1.png) ![Crea estensione](extensions-xf-images/5.walkthrough-run2.png) ![Crea estensione](extensions-xf-images/6.walkthrough-run3.png)

    L'estensione viene attivata e la pagina Novell. Forms viene visualizzata all'utente. Tutte le associazioni e i comandi funzionano come nell'app contenitore.

1. Il controller di visualizzazione del punto di ingresso originale è visibile perché viene creato e attivato da iOS. Per risolvere questo problema, modificare lo stile di presentazione modale in `UIModalPresentationStyle.FullScreen` per il nuovo controller aggiungendo il codice seguente immediatamente prima della `PresentModalViewController` chiamata:

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    Compilazione ed esecuzione nel simulatore iOS o in un dispositivo:

    ![Novell. Forms nell'estensione iOS](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > Per la compilazione del dispositivo assicurarsi di usare le impostazioni di compilazione corrette e la configurazione di **rilascio** , come [descritto qui](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions).

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni iOS in Novell. iOS](~/iOS/platform/extensions.md)
- [Novell. Forms nei progetti nativi Novell](~/xamarin-forms/platform/native-forms.md)
- [Ottimizzare l'efficienza e le prestazioni di un'estensione dell'app iOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [Codice sorgente di esempio](https://github.com/xamcat/xamarin-forms-ios-extension)
