# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Image`](xref:Xamarin.Forms.Image) per personalizzarne l'aspetto:

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Questo codice imposta la proprietà [`Aspect`](xref:Xamarin.Forms.Image.Aspect) che definisce la modalità di ridimensionamento dell'immagine su [`Fill`](xref:Xamarin.Forms.Aspect.Fill). Il membro `Fill` è definito nell'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) e adatta l'immagine alla vista, indipendentemente dalla distorsione dell'immagine. Per altre informazioni sul ridimensionamento delle immagini, vedere [Visualizzazione di immagini](~/xamarin-forms/user-interface/images.md#displaying-images) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    L'estensione di markup `OnPlatform` consente di personalizzare l'aspetto dell'interfaccia utente per ogni piattaforma. In questo esempio l'estensione di markup è usata per impostare le proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) su 300 unità indipendenti dal dispositivo in iOS e su 250 unità indipendenti dal dispositivo in Android. Per altre informazioni sull'estensione di markup `OnPlatform`, vedere [Estensione di markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) nella guida [Uso di estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    Inoltre, la proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) specifica che l'immagine verrà centrata orizzontalmente.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di Image ridimensionata in modo diverso in iOS e Android](../images/customize-appearance.png "Image ridimensionata in base alla piattaforma")](../images/customize-appearance-large.png#lightbox "Image ridimensionata in base alla piattaforma")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Image`](xref:Xamarin.Forms.Image) per personalizzarne l'aspetto:

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Questo codice imposta la proprietà [`Aspect`](xref:Xamarin.Forms.Image.Aspect) che definisce la modalità di ridimensionamento dell'immagine su [`Fill`](xref:Xamarin.Forms.Aspect.Fill). Il membro `Fill` è definito nell'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) e adatta l'immagine alla vista, indipendentemente dalla distorsione dell'immagine. Per altre informazioni sul ridimensionamento delle immagini, vedere [Visualizzazione di immagini](~/xamarin-forms/user-interface/images.md#displaying-images) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    L'estensione di markup `OnPlatform` consente di personalizzare l'aspetto dell'interfaccia utente per ogni piattaforma. In questo esempio l'estensione di markup è usata per impostare le proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) su 300 in iOS e su 250 in Android. Per altre informazioni sull'estensione di markup `OnPlatform`, vedere [Estensione di markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) nella guida [Utilizzo di estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    Inoltre, la proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) specifica che l'immagine verrà centrata orizzontalmente.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di Image ridimensionata in modo diverso in iOS e Android](../images/customize-appearance.png "Image ridimensionata in base alla piattaforma")](../images/customize-appearance-large.png#lightbox "Image ridimensionata in base alla piattaforma")
