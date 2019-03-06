# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **ButtonTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **ButtonTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **ButtonTutorial** in **Esplora soluzioni** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>    
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un [`Button`](xref:Xamarin.Forms.Button) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Button.Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato nel `Button`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un pulsante in iOS e Android](../images/create-button.png "Pulsante contenente testo")](../images/create-button-large.png#lightbox "Pulsante contenente testo")

    Si noti che per impostazione predefinita un controllo [`Button`](xref:Xamarin.Forms.Button) tende a occupare tutto lo spazio consentito, in questo caso l'intera larghezza del relativo padre ([`StackLayout`](xref:Xamarin.Forms.StackLayout)).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **ButtonTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **ButtonTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **ButtonTutorial** nel **riquadro della soluzione** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un [`Button`](xref:Xamarin.Forms.Button) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Button.Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato nel `Button`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un pulsante in iOS e Android](../images/create-button.png "Pulsante contenente testo")](../images/create-button-large.png#lightbox "Pulsante contenente testo")

    Si noti che per impostazione predefinita un controllo [`Button`](xref:Xamarin.Forms.Button) tende a occupare tutto lo spazio consentito, in questo caso l'intera larghezza del relativo padre ([`StackLayout`](xref:Xamarin.Forms.StackLayout)).
