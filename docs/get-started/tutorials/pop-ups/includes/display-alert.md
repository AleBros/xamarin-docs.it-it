---
ms.openlocfilehash: 875f00b379879aa131d37018f89e475170e5320e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "67277298"
---
Xamarin.Forms ha un popup modale, conosciuto come avviso, per avvisare l'utente o per rivolgere semplici domande a un utente. In questo esercizio si userà il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) dalla classe [`Page`](xref:Xamarin.Forms.Page) per visualizzare un avviso all'utente e per porre semplici domande.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **PopupsTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **PopupsTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **PopupsTutorial** in **Esplora soluzioni** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da due oggetti [`Button`](xref:Xamarin.Forms.Button) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Button.Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato in ogni `Button`e gli eventi [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sono impostati per i gestori di eventi che verranno creati nel passaggio successivo.

1. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **PopupsTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnDisplayAlertButtonClicked` e `OnDisplayAlertQuestionButtonClicked` alla classe:

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Quando viene toccato un [`Button`](xref:Xamarin.Forms.Button), viene eseguito il metodo del gestore eventi corrispondente. Il metodo `OnDisplayAlertButtonClicked` chiama il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) per visualizzare un avviso modale con un singolo pulsante Annulla. Dopo che l'avviso viene ignorato l'utente può continuare l'interazione con l'applicazione.

    Il metodo `OnDisplayAlertQuestionButtonClicked` chiama un overload del metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) per visualizzare un avviso modale con un pulsante di conferma e un pulsante Annulla. Dopo che l'utente ha selezionato uno dei pulsanti, la selezione viene restituita come `boolean`.

    > [!IMPORTANT]
    > Il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) è asincrono e deve sempre includere l'attesa con la parola chiave `await`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Quindi, toccare il primo [`Button`](xref:Xamarin.Forms.Button):

    [![Screenshot di un avviso in iOS e Android](../images/alert.png "Avviso")](../images/alert-large.png#lightbox "Avviso")

    Dopo aver ignorato l'avviso, toccare il secondo [`Button`](xref:Xamarin.Forms.Button):

    [![Screenshot di un avviso che pone una domanda, in iOS e Android](../images/alert-question.png "Avviso che pone una domanda")](../images/alert-question-large.png#lightbox "Avviso che pone una domanda")

    Si noti che, dopo aver selezionato una risposta alla domanda, la risposta viene visualizzata nella finestra **Output applicazione**  di Visual Studio.

    Per altre informazioni sulla visualizzazione degli avvisi, vedere [Display an alert](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert) (Visualizzare un avviso) nella guida [Display Pop-ups](~/xamarin-forms/user-interface/pop-ups.md) (Visualizzare i popup).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **PopupsTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **PopupsTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **PopupsTutorial** nel **riquadro della soluzione** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da due oggetti [`Button`](xref:Xamarin.Forms.Button) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Button.Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato in ogni `Button`e gli eventi [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sono impostati per i gestori di eventi che verranno creati nel passaggio successivo.

1. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **PopupsTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnDisplayAlertButtonClicked` e `OnDisplayAlertQuestionButtonClicked` alla classe:

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Quando viene toccato un [`Button`](xref:Xamarin.Forms.Button), viene eseguito il metodo del gestore eventi corrispondente. Il metodo `OnDisplayAlertButtonClicked` chiama il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) per visualizzare un avviso modale con un singolo pulsante Annulla. Dopo che l'avviso viene ignorato l'utente può continuare l'interazione con l'applicazione.

    Il metodo `OnDisplayAlertQuestionButtonClicked` chiama un overload del metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) per visualizzare un avviso modale con un pulsante di conferma e un pulsante Annulla. Dopo che l'utente ha selezionato uno dei pulsanti, la selezione viene restituita come `boolean`.

    > [!IMPORTANT]
    > Il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) è asincrono e deve sempre includere l'attesa con la parola chiave `await`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Quindi, toccare il primo [`Button`](xref:Xamarin.Forms.Button):

    [![Screenshot di un avviso in iOS e Android](../images/alert.png "Avviso")](../images/alert-large.png#lightbox "Avviso")

    Dopo aver ignorato l'avviso, toccare il secondo [`Button`](xref:Xamarin.Forms.Button):

    [![Screenshot di un avviso che pone una domanda, in iOS e Android](../images/alert-question.png "Avviso che pone una domanda")](../images/alert-question-large.png#lightbox "Avviso che pone una domanda")

    Si noti che, dopo aver selezionato una risposta alla domanda, la risposta viene visualizzata nella finestra **Output applicazione**  di Visual Studio per Mac.

    Per altre informazioni sulla visualizzazione degli avvisi, vedere [Display an alert](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert) (Visualizzare un avviso) nella guida [Display Pop-ups](~/xamarin-forms/user-interface/pop-ups.md) (Visualizzare i popup).
