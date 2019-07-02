---
ms.openlocfilehash: 3130c20d39e0140695eed92ffa4941d6bafe796e
ms.sourcegitcommit: b4c9c574b771ae0265171ca5e938aed1c5e35028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67394545"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.XAML** modificare la dichiarazione [`Entry`](xref:Xamarin.Forms.Entry) in modo che imposti un gestore per gli eventi [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed):

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Questo codice imposta l'evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) su un gestore eventi denominato `OnEntryTextChanged` e l'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) su un gestore eventi denominato `OnEntryCompleted`. Entrambi i gestori eventi verranno creati nel passaggio successivo.

1. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **EntryTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnEntryTextChanged` e `OnEntryCompleted` alla classe:

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Quando il testo in [`Entry`](xref:Xamarin.Forms.Entry) cambia, viene eseguito il metodo `OnEntryTextChanged`. L'argomento `sender` è l'oggetto `Entry` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Entry`. L'argomento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornisce i valori del testo nuovo e di quello precedente, prima e dopo la modifica del testo.

    Quando si finalizza il testo in [`Entry`](xref:Xamarin.Forms.Entry) con il tasto INVIO, viene eseguito il metodo `OnEntryCompleted`. L'argomento `sender` è l'oggetto `Entry` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Entry`.

    > [!IMPORTANT]
    > Il testo immesso in [`Entry`](xref:Xamarin.Forms.Entry) viene archiviato nella proprietà [`Text`](xref:Xamarin.Forms.Entry.Text).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot della voce che contiene testo, in iOS e Android](../images/text-changes.png "Voce con il testo")](../images/text-changes-large.png#lightbox "Voce con il testo")

    Impostare i punti di interruzione nei due gestori di eventi e immettere il testo in [`Entry`](xref:Xamarin.Forms.Entry) e osservare la generazione degli eventi [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Per altre informazioni sugli eventi [`Entry`](xref:Xamarin.Forms.Entry), vedere [Eventi e interattività](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) nella guida [Voce Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.XAML** modificare la dichiarazione [`Entry`](xref:Xamarin.Forms.Entry) in modo che imposti un gestore per gli eventi [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed):

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Questo codice imposta l'evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) su un gestore eventi denominato `OnEntryTextChanged` e l'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) su un gestore eventi denominato `OnEntryCompleted`. Entrambi i gestori eventi verranno creati nel passaggio successivo.

1. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **EntryTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnEntryTextChanged` e `OnEntryCompleted` alla classe:

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Quando il testo in [`Entry`](xref:Xamarin.Forms.Entry) cambia, viene eseguito il metodo `OnEntryTextChanged`. L'argomento `sender` è l'oggetto `Entry` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Entry`. L'argomento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornisce i valori del testo nuovo e di quello precedente, prima e dopo la modifica del testo.

    Quando si finalizza il testo in [`Entry`](xref:Xamarin.Forms.Entry) con il tasto INVIO, viene eseguito il metodo `OnEntryCompleted`. L'argomento `sender` è l'oggetto `Entry` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Entry`.

    > [!IMPORTANT]
    > Il testo immesso in [`Entry`](xref:Xamarin.Forms.Entry) viene archiviato nella proprietà [`Text`](xref:Xamarin.Forms.Entry.Text).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot della voce che contiene testo, in iOS e Android](../images/text-changes.png "Voce con il testo")](../images/text-changes-large.png#lightbox "Voce con il testo")

    Impostare i punti di interruzione nei due gestori di eventi e immettere il testo in [`Entry`](xref:Xamarin.Forms.Entry) e osservare la generazione degli eventi [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Per altre informazioni sugli eventi [`Entry`](xref:Xamarin.Forms.Entry), vedere [Eventi e interattività](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) nella guida [Voce Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
