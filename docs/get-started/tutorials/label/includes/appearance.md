---
ms.openlocfilehash: 4fc6c50b5aa2ce502b4157ca2b15f0d33a68ecd1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896683"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Label`](xref:Xamarin.Forms.Label) per modificarne l'aspetto visivo:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Questo codice imposta le proprietà che modificano l'aspetto visivo di [`Label`](xref:Xamarin.Forms.Label). La proprietà [`TextColor`](xref:Xamarin.Forms.Label.TextColor) imposta il colore del testo di `Button`. La proprietà [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) imposta il carattere dell'etichetta su corsivo e la proprietà [`FontSize`](xref:Xamarin.Forms.Label.FontSize) imposta le dimensioni del carattere. Viene anche applicata una decorazione di sottolineatura del testo a `Label` impostando la relativa proprietà [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) e il testo viene centrato orizzontalmente tramite l'impostazione della proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) su [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Si noti che l'aspetto di [`Label`](xref:Xamarin.Forms.Label) è stato modificato:

    [![Screenshot di Label con l'aspetto visivo modificato in iOS e Android](../images/change-label-appearance.png "Label con aspetto modificato")](../images/change-label-appearance-large.png#lightbox "Label con aspetto modificato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Label`](xref:Xamarin.Forms.Label), vedere la guida [Etichetta Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Label`](xref:Xamarin.Forms.Label) per modificarne l'aspetto visivo:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Questo codice imposta le proprietà che modificano l'aspetto visivo di [`Label`](xref:Xamarin.Forms.Label). La proprietà [`TextColor`](xref:Xamarin.Forms.Label.TextColor) imposta il colore del testo di `Button`. La proprietà [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) imposta il carattere dell'etichetta su corsivo e la proprietà [`FontSize`](xref:Xamarin.Forms.Label.FontSize) imposta le dimensioni del carattere. Viene anche applicata una decorazione di sottolineatura del testo a `Label` impostando la relativa proprietà [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) e il testo viene centrato orizzontalmente tramite l'impostazione della proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) su [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Si noti che l'aspetto di [`Label`](xref:Xamarin.Forms.Label) è stato modificato:

    [![Screenshot di Label con l'aspetto visivo modificato in iOS e Android](../images/change-label-appearance.png "Label con aspetto modificato")](../images/change-label-appearance-large.png#lightbox "Label con aspetto modificato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Label`](xref:Xamarin.Forms.Label), vedere la guida [Etichetta Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
