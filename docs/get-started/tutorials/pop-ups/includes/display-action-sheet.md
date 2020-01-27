---
ms.openlocfilehash: 87eb021e6cc571a9a5522697cde2aa11ee991308
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "66193800"
---

Xamarin.Forms ha un popup modale, noto come foglio delle azioni, che può essere usato per guidare gli utenti per l'esecuzione di un'attività. In questo esercizio si userà il metodo [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) dalla classe [`Page`](xref:Xamarin.Forms.Page) per visualizzare un foglio delle azioni che guida gli utenti per l'esecuzione di un'attività.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** aggiungere una nuova dichiarazione [`Button`](xref:Xamarin.Forms.Button) che visualizzerà un foglio delle azioni:

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

     La proprietà [`Button.Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato nel `Button`. Inoltre, l'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) viene impostato su un gestore eventi denominato `OnDisplayActionSheetButtonClicked`, che verrà creato nel passaggio successivo.

1. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **PopupsTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere quindi il gestore eventi `OnDisplayActionSheetButtonClicked` alla classe:

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Con il tocco su [`Button`](xref:Xamarin.Forms.Button) viene eseguito il metodo `OnDisplayActionSheetButtonClicked`. Questo metodo chiama il metodo [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) per presentare all'utente un set di alternative su come procedere per eseguire un'attività. Dopo che l'utente ha selezionato una delle alternative, la selezione viene restituita come `string`.

    > [!IMPORTANT]
    > Il metodo [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) è asincrono e deve sempre includere l'attesa con la parola chiave `await`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Toccare quindi l'elemento [`Button`](xref:Xamarin.Forms.Button) aggiunto a [`ContentPage`](xref:Xamarin.Forms.ContentPage):

    [![Screenshot di un foglio delle azioni, in iOS e Android](../images/actionsheet.png "Foglio delle azioni di supporto degli utenti nelle attività")](../images/actionsheet-large.png#lightbox "Foglio delle azioni di supporto degli utenti nelle attività")

    Si noti che, dopo aver selezionato un'alternativa nella finestra di dialogo del foglio delle azioni, la selezione viene visualizzata nella finestra **Output** di Visual Studio.

    Per altre informazioni sulla visualizzazione dei fogli delle azioni, vedere [Guide users through tasks](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) (Supporto degli utenti nelle attività) nella guida [Display Pop-ups](~/xamarin-forms/user-interface/pop-ups.md) (Visualizzare popup).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** aggiungere una nuova dichiarazione [`Button`](xref:Xamarin.Forms.Button) che visualizzerà un foglio delle azioni:

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

    La proprietà [`Button.Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato nel `Button`. Inoltre, l'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) viene impostato su un gestore eventi denominato `OnDisplayActionSheetButtonClicked`, che verrà creato nel passaggio successivo.

1. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **PopupsTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere il gestore eventi `OnDisplayActionSheetButtonClicked` alla classe:

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Con il tocco su [`Button`](xref:Xamarin.Forms.Button) viene eseguito il metodo `OnDisplayActionSheetButtonClicked`. Questo metodo chiama il metodo [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) per presentare all'utente un set di alternative su come procedere per eseguire un'attività. Dopo che l'utente ha selezionato una delle alternative, la selezione viene restituita come `string`.

    > [!IMPORTANT]
    > Il metodo [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) è asincrono e deve sempre includere l'attesa con la parola chiave `await`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Toccare quindi l'elemento [`Button`](xref:Xamarin.Forms.Button) aggiunto a [`ContentPage`](xref:Xamarin.Forms.ContentPage):

    [![Screenshot di un foglio delle azioni, in iOS e Android](../images/actionsheet.png "Foglio delle azioni di supporto degli utenti nelle attività")](../images/actionsheet-large.png#lightbox "Foglio delle azioni di supporto degli utenti nelle attività")

    Si noti che, dopo aver selezionato un'alternativa nella finestra di dialogo del foglio delle azioni, la selezione viene visualizzata nella finestra **Output applicazione**  di Visual Studio per Mac.

    Per altre informazioni sulla visualizzazione dei fogli delle azioni, vedere [Guide users through tasks](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) (Supporto degli utenti nelle attività) nella guida [Display Pop-ups](~/xamarin-forms/user-interface/pop-ups.md) (Visualizzare popup).
