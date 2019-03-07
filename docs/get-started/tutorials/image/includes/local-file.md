È possibile aggiungere file di immagine ai progetti della piattaforma e si può farvi riferimento dal codice Xamarin.Forms condiviso. Questo metodo di distribuzione delle immagini è necessario quando le immagini sono specifiche della piattaforma, ad esempio quando si usano risoluzioni diverse su piattaforme diverse o progettazioni leggermente differenti.

In questo esercizio si modificherà la soluzione **ImageTutorial** per visualizzare un'immagine locale anziché un'immagine scaricata da un URI. L'immagine locale è il logo di Xamarin che deve essere scaricato facendo clic sul pulsante seguente.

> [!div class="nextstepaction"]
> [Scaricare XamarinLogo.png](https://raw.githubusercontent.com/xamarin/xamarin-forms-samples/master/UserInterface/PlatformSpecifics/Droid/Resources/drawable/XamarinLogo.png)

> [!IMPORTANT]
> Per usare la stessa immagine su tutte le piattaforme *si deve usare il medesimo nome di file in tutte le piattaforme* e deve trattarsi di un nome di risorsa di Android valido, ad esempio sono ammesse solo lettere minuscole, numeri, il carattere di sottolineatura e il punto.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **Esplora soluzioni**, nel progetto **ImageTutorial.iOS**, espandere **Cataloghi asset** e fare doppio clic su **Asset** per aprirlo. Nella scheda **Assets.xcassets** fare clic sul pulsante **Più** e selezionare **Aggiungi set di immagini**:

    ![Screenshot della creazione di un nuovo set di immagini nel catalogo asset in Visual Studio](../images/vs/new-image-set.png "Nuovo set di immagini del catalogo asset")

1. Nella scheda **Assets.xcassets** selezionare il nuovo set di immagini e verrà visualizzato l'editor:

    ![Screenshot del nuovo set di immagini nel catalogo asset in Visual Studio](../images/vs/new-image-set-editor.png "Editor del set di immagini del catalogo asset")

1. Trascinare **XamarinLogo.png** dal file system alla casella **1x** per la categoria **Universale**:

    ![Screenshot del set di immagini che contiene un'immagine in Visual Studio](../images/vs/image-set-with-image.png "Set di immagini contenente un'immagine")

1. Nella scheda **Assets.xcassets** fare clic sul nome del nuovo set di immagini e rinominarlo **XamarinLogo**:

    ![Screenshot del set di immagini rinominato in Visual Studio](../images/vs/rename-image-set.png "Set di immagini rinominato")

    Salvare e chiudere la scheda **Assets.xcassets**.

1. In **Esplora soluzioni**, nel progetto **ImageTutorial.Android**, espandere la cartella **Risorse**. Quindi trascinare **XamarinLogo.png** dal file system alla cartella **drawable**:

    ![Screenshot del file di immagine come risorsa Android in Visual Studio](../images/vs/android-resource.png "File di immagine locale nella cartella delle risorse Android")

    > [!NOTE]
    > Visual Studio imposterà automaticamente l'azione di compilazione per l'immagine su **AndroidResource**.

1. Nel progetto **ImageTutorial**, in **MainPage.xaml**, modificare la dichiarazione [`Image`](xref:Xamarin.Forms.Editor) in modo che visualizzi il file locale **XamarinLogo**:

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Questo codice imposta la proprietà [`Source`](xref:Xamarin.Forms.Image.Source) del file locale da visualizzare. La proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) è impostata su 300 unità indipendenti dal dispositivo in iOS e 250 unità indipendenti dal dispositivo in Android. Inoltre, la proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) specifica che l'immagine verrà centrata orizzontalmente.

    > [!NOTE]
    > Per le immagini PNG in iOS, l'estensione **.png** può essere omessa dal nome del file specificato nella proprietà [`Source`](xref:Xamarin.Forms.Image.Source). Per altri formati di immagine è richiesta l'estensione.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di una vista Image che mostra un'immagine locale in iOS e Android](../images/local-file.png "Vista Image che mostra un'immagine locale")](../images/local-file-large.png#lightbox "Vista Image che mostra un'immagine locale")

    Per altre informazioni sulle immagini locali, vedere [Immagini locali](~/xamarin-forms/user-interface/images.md#local-images) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **riquadro della soluzione**, nel progetto **ImageTutorial.iOS**, fare doppio clic su **Assets.xcassets** per aprirlo. Quindi, nell'**elenco Asset** fare doppio clic e selezionare **New Image Set** (Nuovo set di immagini):

    ![Screenshot della creazione di un nuovo set di immagini nel catalogo asset in Visual Studio per Mac](../images/vsmac/new-image-set.png "Nuovo set di immagini del catalogo asset")

1. Nell'**elenco Asset** selezionare il nuovo set di immagini e verrà visualizzato l'editor:

    ![Screenshot del nuovo set di immagini nel catalogo asset in Visual Studio per Mac](../images/vsmac/new-image-set-editor.png "Editor del set di immagini del catalogo asset")

1. Trascinare **XamarinLogo.png** dal file system alla casella **1x** per la categoria **Universale**:

    ![Screenshot del set di immagini che contiene un'immagine in Visual Studio per Mac](../images/vsmac/image-set-with-image.png "Set di immagini contenente un'immagine")

1. Nell'**elenco Asset** fare doppio clic sul nome del nuovo set di immagini e rinominarlo **XamarinLogo**:

    ![Screenshot del set di immagini rinominato in Visual Studio per Mac](../images/vsmac/rename-image-set.png "Set di immagini rinominato")

1. Nel **riquadro della soluzione**, nel progetto **ImageTutorial.Android**, espandere la cartella **Risorse**. Quindi trascinare **XamarinLogo.png** dal file system alla cartella **drawable**.

1. Nella finestra di dialogo **Add File to Folder** (Aggiungi file a cartella) selezionare **OK**.

    ![Screenshot del file di immagine come risorsa Android in Visual Studio per Mac](../images/vsmac/android-resource.png "File di immagine locale nella cartella delle risorse Android")

    > [!NOTE]
    > Visual Studio per Mac imposterà automaticamente l'azione di compilazione per l'immagine su **AndroidResource**.

1. Nel progetto **ImageTutorial**, in **MainPage.xaml**, modificare la dichiarazione [`Image`](xref:Xamarin.Forms.Editor) in modo che visualizzi il file locale **XamarinLogo**:

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Questo codice imposta la proprietà [`Source`](xref:Xamarin.Forms.Image.Source) del file locale da visualizzare. La proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) è impostata su 300 unità indipendenti dal dispositivo in iOS e 250 unità indipendenti dal dispositivo in Android. Inoltre, la proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) specifica che l'immagine verrà centrata orizzontalmente.

    > [!NOTE]
    > Per le immagini PNG in iOS, l'estensione **.png** può essere omessa dal nome del file specificato nella proprietà [`Source`](xref:Xamarin.Forms.Image.Source). Per altri formati di immagine è richiesta l'estensione.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di una vista Image che mostra un'immagine locale in iOS e Android](../images/local-file.png "Vista Image che mostra un'immagine locale")](../images/local-file-large.png#lightbox "Vista Image che mostra un'immagine locale")

    Per altre informazioni sulle immagini locali, vedere [Immagini locali](~/xamarin-forms/user-interface/images.md#local-images) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
