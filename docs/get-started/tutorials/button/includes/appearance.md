---
ms.openlocfilehash: 1d2bed830af97ce1ff329a5396a415247a43189d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372946"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Button`](xref:Xamarin.Forms.Button) per modificarne l'aspetto visivo:

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Questo codice imposta le proprietà che modificano l'aspetto visivo di [`Button`](xref:Xamarin.Forms.Button). La proprietà [`TextColor`](xref:Xamarin.Forms.Button.TextColor) imposta il colore del testo di `Button`, mentre la proprietà [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) imposta il colore dello sfondo del testo. La proprietà [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) imposta il colore di un'area che circonda `Button`, mentre la proprietà [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) imposta la larghezza del bordo. Per impostazione predefinita, `Button` è rettangolare ma è possibile arrotondarne gli angoli impostando la proprietà [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) su un valore appropriato. Inoltre, le dimensioni di `Button` vengono modificate impostando le relative proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Si noti che l'aspetto di [`Button`](xref:Xamarin.Forms.Button) è stato modificato:

    [![Screenshot di un pulsante con aspetto visivo modificato, in iOS e Android](../images/change-button-appearance.png "Pulsante con aspetto modificato")](../images/change-button-appearance-large.png#lightbox "Pulsante con aspetto modificato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Button`](xref:Xamarin.Forms.Button), vedere [Aspetto del pulsante](~/xamarin-forms/user-interface/button.md#button-appearance) nella guida [Pulsante Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Button`](xref:Xamarin.Forms.Button) per modificarne l'aspetto visivo:

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Questo codice imposta le proprietà che modificano l'aspetto visivo di [`Button`](xref:Xamarin.Forms.Button). La proprietà [`TextColor`](xref:Xamarin.Forms.Button.TextColor) imposta il colore del testo di `Button`, mentre la proprietà [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) imposta il colore dello sfondo del testo. La proprietà [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) imposta il colore di un'area che circonda `Button`, mentre la proprietà [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) imposta la larghezza del bordo. Per impostazione predefinita, `Button` è rettangolare ma è possibile arrotondarne gli angoli impostando la proprietà [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) su un valore appropriato. Inoltre, le dimensioni di `Button` vengono modificate impostando le relative proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Si noti che l'aspetto di [`Button`](xref:Xamarin.Forms.Button) è stato modificato:

    [![Screenshot di un pulsante con aspetto visivo modificato, in iOS e Android](../images/change-button-appearance.png "Pulsante con aspetto modificato")](../images/change-button-appearance-large.png#lightbox "Pulsante con aspetto modificato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Button`](xref:Xamarin.Forms.Button), vedere [Aspetto del pulsante](~/xamarin-forms/user-interface/button.md#button-appearance) nella guida [Pulsante Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
