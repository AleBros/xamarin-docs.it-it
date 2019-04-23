---
ms.openlocfilehash: 841dac9486097e27923ccfe582803b4ec50371cf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896675"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Label`](xref:Xamarin.Forms.Label) per visualizzare testo in più formati in una singola classe `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Questo codice visualizza il testo in una singola classe [`Label`](xref:Xamarin.Forms.Label) con più formati. Il testo nella prima classe [`Span`](xref:Xamarin.Forms.Span) è visualizzato con la formattazione impostata nella classe `Label`, mentre il testo nella seconda e terza istanza di `Span` è visualizzato con la formattazione impostata nella classe `Label` e la formattazione aggiuntiva impostata in ogni `Span`.

    > [!NOTE]
    > La proprietà [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) è di tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString) che comprende una o più istanze di [`Span`](xref:Xamarin.Forms.Span).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Si noti che l'aspetto di [`Label`](xref:Xamarin.Forms.Label) è stato modificato:

    [![Screenshot di Label con testo formattato in iOS e Android](../images/label-formatted-text.png "Label con testo formattato")](../images/label-formatted-text-large.png#lightbox "Label con testo formattato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Span`](xref:Xamarin.Forms.Span), vedere [Testo formattato](~/xamarin-forms/user-interface/text/label.md#formatted-text) nella guida [Etichetta Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Label`](xref:Xamarin.Forms.Label) per visualizzare testo in più formati in una singola classe `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Questo codice visualizza il testo in una singola classe [`Label`](xref:Xamarin.Forms.Label) con più formati. Il testo nella prima classe [`Span`](xref:Xamarin.Forms.Span) è visualizzato con la formattazione impostata nella classe `Label`, mentre il testo nella seconda e terza istanza di `Span` è visualizzato con la formattazione impostata nella classe `Label` e la formattazione aggiuntiva impostata in ogni `Span`.

    > [!NOTE]
    > La proprietà [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) è di tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString) che comprende una o più istanze di [`Span`](xref:Xamarin.Forms.Span).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Si noti che l'aspetto di [`Label`](xref:Xamarin.Forms.Label) è stato modificato:

    [![Screenshot di Label con testo formattato in iOS e Android](../images/label-formatted-text.png "Label con testo formattato")](../images/label-formatted-text-large.png#lightbox "Label con testo formattato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Span`](xref:Xamarin.Forms.Span), vedere [Testo formattato](~/xamarin-forms/user-interface/text/label.md#formatted-text) nella guida [Etichetta Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
