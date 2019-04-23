---
ms.openlocfilehash: bd3f37082443e93f10f60d9466fe22aae8571b14
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373445"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Editor`](xref:Xamarin.Forms.Editor) per personalizzarne il comportamento:

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Questo codice imposta le proprietà che personalizzano il comportamento di [`Editor`](xref:Xamarin.Forms.Editor). La proprietà [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) è impostata su [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), che indica che l'altezza di `Editor` aumenterà quando è riempito con del testo e diminuirà quando il testo viene rimosso. La proprietà [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la lunghezza di input valida per `Editor`. Inoltre, la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false` per disabilitare il controllo ortografico, mentre la proprietà `IsTextPredictionEnabled` è impostata su `false` per disabilitare il completamento del testo e il completamento automatico del testo.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Immettere il testo in [`Editor`](xref:Xamarin.Forms.Entry) e osservare che l'altezza di `Editor` aumenta man mano che viene compilato con il testo e diminuisce man mano che il testo viene rimosso e che il numero massimo di caratteri che è possibile immettere è 200:

    [![Screenshot di un Editor di ridimensionamento automatico, in iOS e Android](../images/customize-behavior.png "Editor di ridimensionamento automatico")](../images/customize-behavior-large.png#lightbox "Editor di ridimensionamento automatico")

    Per altre informazioni sulla personalizzazione del comportamento [`Editor`](xref:Xamarin.Forms.Editor), vedere la guida [Editor Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Editor`](xref:Xamarin.Forms.Editor) per personalizzarne il comportamento:

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Questo codice imposta le proprietà che personalizzano il comportamento di [`Editor`](xref:Xamarin.Forms.Editor). La proprietà [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) è impostata su [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), che indica che l'altezza di `Editor` aumenterà quando è riempito con del testo e diminuirà quando il testo viene rimosso. La proprietà [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la lunghezza di input valida per `Editor`. Inoltre, la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false` per disabilitare il controllo ortografico, mentre la proprietà `IsTextPredictionEnabled` è impostata su `false` per disabilitare il completamento del testo e il completamento automatico del testo.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Immettere il testo in [`Editor`](xref:Xamarin.Forms.Entry) e osservare che l'altezza di `Editor` aumenta man mano che viene compilato con il testo e diminuisce man mano che il testo viene rimosso e che il numero massimo di caratteri che è possibile immettere è 200:

    [![Screenshot di un Editor di ridimensionamento automatico, in iOS e Android](../images/customize-behavior.png "Editor di ridimensionamento automatico")](../images/customize-behavior-large.png#lightbox "Editor di ridimensionamento automatico")

    Per altre informazioni sulla personalizzazione del comportamento [`Editor`](xref:Xamarin.Forms.Editor), vedere la guida [Editor Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
