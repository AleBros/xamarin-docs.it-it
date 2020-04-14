---
ms.openlocfilehash: 48af50d31013f696879174a5cf108ab9fde92d0b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "61343464"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Entry`](xref:Xamarin.Forms.Entry) per personalizzarne il comportamento:

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Questo codice imposta le proprietà che personalizzano il comportamento di [`Entry`](xref:Xamarin.Forms.Entry). La proprietà [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la lunghezza di input valida per `Entry`e la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false` per disabilitare il controllo ortografico. Analogamente, la proprietà [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) è impostata su `false` per disabilitare il completamento del testo e il completamento automatico del testo. Inoltre, la proprietà [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) garantisce che i caratteri immessi vengono mascherati con un carattere della password (un cerchio nero).

    > [!NOTE]
    > Per alcuni scenari di immissione del testo, quali l'immissione di una password, il controllo ortografico e il completamento del testo offre un'esperienza negativa e pertanto deve essere disabilitata.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Immettere il testo in [`Entry`](xref:Xamarin.Forms.Entry) e osservare che ogni carattere viene sostituito con un carattere della maschera della password e che il numero massimo di caratteri che possono essere immessi è 15:

    [![Screenshot di una voce con testo mascherato con caratteri della password, in iOS e Android](../images/customize-behavior.png "Voce con caratteri della password mascherati")](../images/customize-behavior-large.png#lightbox "Voce con caratteri della password mascherati")

    Per altre informazioni sulla personalizzazione del comportamento [`Entry`](xref:Xamarin.Forms.Entry), vedere la guida [Voce Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Entry`](xref:Xamarin.Forms.Entry) per personalizzarne il comportamento:

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Questo codice imposta le proprietà che personalizzano il comportamento di [`Entry`](xref:Xamarin.Forms.Entry). La proprietà [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la lunghezza di input valida per `Entry`e la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false` per disabilitare il controllo ortografico. Analogamente, la proprietà [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) è impostata su `false` per disabilitare il completamento del testo e il completamento automatico del testo. Inoltre, la proprietà [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) garantisce che i caratteri immessi vengono mascherati con un carattere della password (un cerchio nero).

    > [!NOTE]
    > Per alcuni scenari di immissione del testo, quali l'immissione di una password, il controllo ortografico e il completamento del testo offre un'esperienza negativa e pertanto deve essere disabilitata.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Immettere il testo in [`Entry`](xref:Xamarin.Forms.Entry) e osservare che ogni carattere viene sostituito con un carattere della maschera della password e che il numero massimo di caratteri che possono essere immessi è 15:

    [![Screenshot di una voce con testo mascherato con caratteri della password, in iOS e Android](../images/customize-behavior.png "Voce con caratteri della password mascherati")](../images/customize-behavior-large.png#lightbox "Voce con caratteri della password mascherati")

    Per altre informazioni sulla personalizzazione del comportamento [`Entry`](xref:Xamarin.Forms.Entry), vedere la guida [Voce Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
