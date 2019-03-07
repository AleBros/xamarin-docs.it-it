In questo esercizio si creerà un'interfaccia utente per l'utilizzo delle classi di accesso ai dati create in precedenza.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su **MainPage.xaml** nel progetto **LocalDatabaseTutorial** in **Esplora soluzioni** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da due istanze [`Entry`](xref:Xamarin.Forms.Entry), un [`Button`](xref:Xamarin.Forms.Button) e un [`ListView`](xref:Xamarin.Forms.ListView) in un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Ogni `Entry` ha la propria proprietà [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) impostata, che specifica il testo segnaposto che viene visualizzato prima dell'input dell'utente. `Button` imposta il relativo evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) su un gestore eventi denominato `OnButtonClicked`, che verrà creato nel passaggio successivo. `ListView` imposta la proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che usa [`TextCell`](xref:Xamarin.Forms.TextCell) per definire l'aspetto di ciascuna riga in [`ListView`](xref:Xamarin.Forms.ListView). I dati `TextCell` associano le proprie proprietà [`Text`](xref:Xamarin.Forms.TextCell.Text) e [`Detail`](xref:Xamarin.Forms.TextCell.Detail) alle proprietà `Name` e `Age` di ciascun oggetto `Person`, rispettivamente.

    Inoltre, le istanze [`Entry`](xref:Xamarin.Forms.Entry) e [`ListView`](xref:Xamarin.Forms.ListView) hanno nomi specificati con l'attributo `x:Name`. Ciò consente al file code-behind di accedere a questi oggetti usando i nomi assegnati.

1. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **LocalDatabaseTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere quindi l'override `OnAppearing` e il gestore eventi `OnButtonClicked` alla classe:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    Il metodo `OnAppearing` popola [`ListView`](xref:Xamarin.Forms.ListView) con tutti i dati archiviati nel database. Il metodo `OnButtonClicked`, che viene eseguito quando viene toccato [`Button`](xref:Xamarin.Forms.Button), salva i dati immessi nel database prima della cancellazione di entrambe le istanze [`Entry`](xref:Xamarin.Forms.Entry) e l'aggiornamento dei dati in `ListView`.

    > [!NOTE]
    > L'override del metodo `OnAppearing` viene eseguito dopo che è stato eseguito il layout dell'elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage), ma subito prima che diventi visibile. Pertanto, questo è un buon momento per impostare il contenuto delle viste Xamarin.Forms.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto.

    Immettere diversi elementi di dati, se si tocca [`Button`](xref:Xamarin.Forms.Button) per ogni elemento di dati. Ciò salverà i dati nel database e ripopolerà [`ListView`](xref:Xamarin.Forms.ListView) con tutti i dati dei database:

    [![Screenshot della persistenza di dati del database SQLite.NET locale, in iOS e Android](../images/consume-data-access-classes.png "Persistenza dei dati del database locale")](../images/consume-data-access-classes-large.png#lightbox "Persistenza dei dati del database locale")

    Per altre informazioni sui database locali in Xamarin.Forms, vedere [Database locali di Xamarin.Forms (Guida)](~/xamarin-forms/app-fundamentals/databases.md)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su **MainPage.xaml** nel progetto **LocalDatabaseTutorial** in **Riquadro della soluzione** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da due istanze [`Entry`](xref:Xamarin.Forms.Entry), un [`Button`](xref:Xamarin.Forms.Button) e un [`ListView`](xref:Xamarin.Forms.ListView) in un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Ogni `Entry` ha la propria proprietà [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) impostata, che specifica il testo segnaposto che viene visualizzato prima dell'input dell'utente. `Button` imposta il relativo evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) su un gestore eventi denominato `OnButtonClicked`, che verrà creato nel passaggio successivo. `ListView` imposta la proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che usa [`TextCell`](xref:Xamarin.Forms.TextCell) per definire l'aspetto di ciascuna riga in [`ListView`](xref:Xamarin.Forms.ListView). I dati `TextCell` associano le proprie proprietà [`Text`](xref:Xamarin.Forms.TextCell.Text) e [`Detail`](xref:Xamarin.Forms.TextCell.Detail) alle proprietà `Name` e `Age` di ciascun oggetto `Person`, rispettivamente.

    Inoltre, le istanze [`Entry`](xref:Xamarin.Forms.Entry) e [`ListView`](xref:Xamarin.Forms.ListView) hanno nomi specificati con l'attributo `x:Name`. Ciò consente al file code-behind di accedere a questi oggetti usando i nomi assegnati.

1. In **Riquadro della soluzione** espandere **MainPage.xaml** nel progetto **LocalDatabaseTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere quindi l'override `OnAppearing` e il gestore eventi `OnButtonClicked` alla classe:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    Il metodo `OnAppearing` popola [`ListView`](xref:Xamarin.Forms.ListView) con tutti i dati archiviati nel database. Il metodo `OnButtonClicked`, che viene eseguito quando viene toccato [`Button`](xref:Xamarin.Forms.Button), salva i dati immessi nel database prima della cancellazione di entrambe le istanze [`Entry`](xref:Xamarin.Forms.Entry) e l'aggiornamento dei dati in `ListView`.

    > [!NOTE]
    > L'override del metodo `OnAppearing` viene eseguito dopo che è stato eseguito il layout dell'elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage), ma subito prima che diventi visibile. Pertanto, questo è un buon momento per impostare il contenuto delle viste Xamarin.Forms.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto.

    Immettere diversi elementi di dati, se si tocca [`Button`](xref:Xamarin.Forms.Button) per ogni elemento di dati. Ciò salverà i dati nel database e ripopolerà [`ListView`](xref:Xamarin.Forms.ListView) con tutti i dati dei database:

    [![Screenshot della persistenza di dati del database SQLite.NET locale, in iOS e Android](../images/consume-data-access-classes.png "Persistenza dei dati del database locale")](../images/consume-data-access-classes-large.png#lightbox "Persistenza dei dati del database locale")

    Per altre informazioni sui database locali in Xamarin.Forms, vedere [Database locali di Xamarin.Forms (Guida)](~/xamarin-forms/app-fundamentals/databases.md)
