# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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

    [![Screenshot di Button con l'aspetto visivo modificato in iOS e Android](../images/change-button-appearance.png "Button con aspetto modificato")](../images/change-button-appearance-large.png#lightbox "Button con aspetto modificato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Button`](xref:Xamarin.Forms.Button), vedere [Aspetto del pulsante](~/xamarin-forms/user-interface/button.md#button-appearance) nella guida [Pulsante Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

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

    [![Screenshot di Button con l'aspetto visivo modificato in iOS e Android](../images/change-button-appearance.png "Button con aspetto modificato")](../images/change-button-appearance-large.png#lightbox "Button con aspetto modificato")

    Per altre informazioni sull'impostazione dell'aspetto di [`Button`](xref:Xamarin.Forms.Button), vedere [Aspetto del pulsante](~/xamarin-forms/user-interface/button.md#button-appearance) nella guida [Pulsante Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
