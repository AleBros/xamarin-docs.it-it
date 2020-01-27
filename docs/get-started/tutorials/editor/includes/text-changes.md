---
ms.openlocfilehash: 19afeed47f1c06c89c58dfd996d360698b19fc9c
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "61373386"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.XAML** modificare la dichiarazione [`Editor`](xref:Xamarin.Forms.Editor) in modo che imposti un gestore per gli eventi [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged) e [`Completed`](xref:Xamarin.Forms.Editor.Completed):

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Questo codice imposta l'evento [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged) su un gestore eventi denominato `OnEditorTextChanged` e l'evento [`Completed`](xref:Xamarin.Forms.Editor.Completed) su un gestore eventi denominato `OnEditorCompleted`. Entrambi i gestori eventi verranno creati nel passaggio successivo.

1. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **EditorTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnEditorTextChanged` e `OnEditorCompleted` alla classe:

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Quando il testo in [`Editor`](xref:Xamarin.Forms.Editor) cambia, viene eseguito il metodo `OnEditorTextChanged`. L'argomento `sender` è l'oggetto `Editor` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Editor`. L'argomento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornisce i valori del testo nuovo e di quello precedente, prima e dopo la modifica del testo.

    Quando viene completato il processo di modifica, viene eseguito il metodo `OnEditorCompleted`. Ciò si ottiene non focalizzando su [`Editor`](xref:Xamarin.Forms.Editor), o anche facendo clic sul pulsante "Fine" in iOS. L'argomento `sender` è l'oggetto `Editor` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Editor`.

    > [!IMPORTANT]
    > Il testo immesso in [`Editor`](xref:Xamarin.Forms.Editor) viene archiviato nella proprietà [`Text`](xref:Xamarin.Forms.Editor.Text).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot dell'editor contenente testo, in iOS e Android](../images/text-changes.png "Editor con testo")](../images/text-changes-large.png#lightbox "Editor con testo")

    Impostare i punti di interruzione nei due gestori di eventi, immettere il testo in [`Editor`](xref:Xamarin.Forms.Editor) e osservare la generazione dell'evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged). Non focalizzare `Editor` per osservare generazione dell'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Per altre informazioni sugli eventi [`Editor`](xref:Xamarin.Forms.Editor), vedere [Interattività](~/xamarin-forms/user-interface/text/editor.md#interactivity) nella guida [Editor Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.XAML** modificare la dichiarazione [`Editor`](xref:Xamarin.Forms.Editor) in modo che imposti un gestore per gli eventi [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged) e [`Completed`](xref:Xamarin.Forms.Editor.Completed):

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Questo codice imposta l'evento [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged) su un gestore eventi denominato `OnEditorTextChanged` e l'evento [`Completed`](xref:Xamarin.Forms.Editor.Completed) su un gestore eventi denominato `OnEditorCompleted`. Entrambi i gestori eventi verranno creati nel passaggio successivo.

1. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **EditorTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnEditorTextChanged` e `OnEditorCompleted` alla classe:

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Quando il testo in [`Editor`](xref:Xamarin.Forms.Editor) cambia, viene eseguito il metodo `OnEditorTextChanged`. L'argomento `sender` è l'oggetto `Editor` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Editor`. L'argomento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fornisce i valori del testo nuovo e di quello precedente, prima e dopo la modifica del testo.

    Quando viene completato il processo di modifica, viene eseguito il metodo `OnEditorCompleted`. Ciò si ottiene non focalizzando su [`Editor`](xref:Xamarin.Forms.Editor), o anche facendo clic sul pulsante "Fine" in iOS. L'argomento `sender` è l'oggetto `Editor` responsabile dell'attivazione dell'evento `TextChanged` e può essere usato per accedere all'oggetto `Editor`.

    > [!IMPORTANT]
    > Il testo immesso in [`Editor`](xref:Xamarin.Forms.Editor) viene archiviato nella proprietà [`Text`](xref:Xamarin.Forms.Editor.Text).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot dell'editor contenente testo, in iOS e Android](../images/text-changes.png "Editor con testo")](../images/text-changes-large.png#lightbox "Editor con testo")

    Impostare i punti di interruzione nei due gestori di eventi, immettere il testo in [`Editor`](xref:Xamarin.Forms.Editor) e osservare la generazione dell'evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged). Non focalizzare `Editor` per osservare generazione dell'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Per altre informazioni sugli eventi [`Editor`](xref:Xamarin.Forms.Editor), vedere [Interattività](~/xamarin-forms/user-interface/text/editor.md#interactivity) nella guida [Editor Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
