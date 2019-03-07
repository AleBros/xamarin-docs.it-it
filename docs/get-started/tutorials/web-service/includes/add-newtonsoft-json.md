# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **WebServiceTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **WebServiceTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. In **Esplora soluzioni** selezionare il progetto **WebServiceTutorial**, fare clic con il pulsante destro del mouse e selezionare **Gestisci pacchetti NuGet...**:

    ![Screenshot della voce di menu Aggiungi pacchetti NuGet che viene selezionata](../images/vs/add-nuget-packages.png "Voce di menu Aggiungi pacchetti NuGet")

1. In **Gestione pacchetti NuGet** selezionare la scheda **Sfoglia**, cercare il pacchetto NuGet **Newtonsoft.Json**, selezionarlo e fare clic sul pulsante **Installa**per aggiungerlo al progetto:

    ![Schermata del pacchetto NuGet Newtonsoft.JSON in Gestione pacchetti NuGet](../images/vs/add-package.png "pacchetto NuGet newtonsoft. JSON")

    Il pacchetto verrà usato per integrare la deserializzazione JSON nell'applicazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **WebServiceTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **WebServiceTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. Nel **riquadro della soluzione** selezionare il progetto **WebServiceTutorial**, fare clic con il pulsante destro del mouse e selezionare **Aggiungi > Gestisci pacchetti NuGet...**:

    ![Screenshot della voce di menu Aggiungi pacchetti NuGet che viene selezionata](../images/vsmac/add-nuget-packages.png "Voce di menu Aggiungi pacchetti NuGet")

1. Nella finestra **Aggiungi pacchetti** cercare il pacchetto NuGet **Newtonsoft.Json**, selezionarlo e fare clic sul pulsante **Aggiungi pacchetto** per aggiungerlo al progetto:

    ![Schermata del pacchetto NuGet Newtonsoft.JSON in Gestione pacchetti NuGet](../images/vsmac/add-package.png "pacchetto NuGet newtonsoft. JSON")

    Il pacchetto verrà usato per integrare la deserializzazione JSON nell'applicazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.
