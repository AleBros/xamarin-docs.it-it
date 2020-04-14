---
ms.openlocfilehash: d1f7d209eaaca62a55b768646f51024609057a63
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372947"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Button`](xref:Xamarin.Forms.Button) in modo che imposti un gestore per l'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked):

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Questo codice imposta l'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) su un gestore eventi denominato `OnButtonClicked` che verrà creato nel passaggio successivo.

1. In **Esplora soluzioni**, nel progetto **ButtonTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere il gestore eventi `OnButtonClicked` alla classe:

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Quando si tocca [`Button`](xref:Xamarin.Forms.Button) viene eseguito il metodo `OnButtonClicked`. L'argomento `sender` è l'oggetto `Button` responsabile dell'attivazione dell'evento `Clicked` e può essere usato per accedere all'oggetto `Button`. Questo gestore dell'evento aggiorna il testo visualizzato dall'elemento `Button`.

    > [!NOTE]
    > Oltre all'evento `Clicked`, `Button` definisce anche gli eventi [`Pressed`](xref:Xamarin.Forms.Button.Pressed) e [`Released`](xref:Xamarin.Forms.Button.Released). Per altre informazioni, vedere [Pressing and releasing the button](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) (Premere e rilasciare il pulsante) nella guida [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md) (Button di Xamarin.Forms).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Fare clic sull'elemento [`Button`](xref:Xamarin.Forms.Button) e osservare che il testo mostrato da esso cambia:

    [![Screenshot del testo del pulsante che cambia dopo la ricezione di un clic, in iOS e Android](../images/handle-button-click.png "Gestire un clic su un pulsante")](../images/handle-button-click-large.png#lightbox "Gestire un clic su un pulsante")

    Per altre informazioni sulla gestione dei clic sui pulsanti, vedere [Handling button clicks](~/xamarin-forms/user-interface/button.md#handling-button-clicks) (Gestione dei clic sui pulsanti) nella guida [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md) (Button di Xamarin.Forms).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Button`](xref:Xamarin.Forms.Button) in modo che imposti un gestore per l'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked):

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Questo codice imposta l'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) su un gestore eventi denominato `OnButtonClicked` che verrà creato nel passaggio successivo.

1. Nel **riquadro della soluzione**, nel progetto **ButtonTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere il gestore eventi `OnButtonClicked` alla classe:

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Quando si tocca [`Button`](xref:Xamarin.Forms.Button) viene eseguito il metodo `OnButtonClicked`. L'argomento `sender` è l'oggetto `Button` responsabile dell'attivazione dell'evento `Clicked` e può essere usato per accedere all'oggetto `Button`. Questo gestore dell'evento aggiorna il testo visualizzato dall'elemento `Button`.

    > [!NOTE]
    > Oltre all'evento `Clicked`, `Button` definisce anche gli eventi [`Pressed`](xref:Xamarin.Forms.Button.Pressed) e [`Released`](xref:Xamarin.Forms.Button.Released). Per altre informazioni, vedere [Pressing and releasing the button](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) (Premere e rilasciare il pulsante) nella guida [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md) (Button di Xamarin.Forms).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Fare clic sull'elemento [`Button`](xref:Xamarin.Forms.Button) e osservare che il testo mostrato da esso cambia:

    [![Screenshot del testo del pulsante che cambia dopo la ricezione di un clic, in iOS e Android](../images/handle-button-click.png "Gestire un clic su un pulsante")](../images/handle-button-click-large.png#lightbox "Gestire un clic su un pulsante")

    Per altre informazioni sulla gestione dei clic sui pulsanti, vedere [Handling button clicks](~/xamarin-forms/user-interface/button.md#handling-button-clicks) (Gestione dei clic sui pulsanti) nella guida [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md) (Button di Xamarin.Forms).
