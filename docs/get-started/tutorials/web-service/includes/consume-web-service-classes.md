---
ms.openlocfilehash: fe38e13ff4b6c89905769d5ef0e5163a538e1e86
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388888"
---
In questo esercizio si creerà un'interfaccia utente per utilizzare la classe `RestService`, che a sua volta recupera i dati dall'API Web [OpenWeatherMap](https://openweathermap.org/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su **MainPage.xaml** nel progetto **WebServiceTutorial** in **Esplora soluzioni** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Entry`](xref:Xamarin.Forms.Entry), un elemento [`Button`](xref:Xamarin.Forms.Button) e una serie di istanze di [`Label`](xref:Xamarin.Forms.Label) in un elemento [`Grid`](xref:Xamarin.Forms.Grid). L'elemento `Entry` viene prepopolato con "Seattle" tramite l'impostazione della relativa proprietà [`Text`](xref:Xamarin.Forms.Entry.Text). `Button` imposta il relativo evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) su un gestore eventi denominato `OnButtonClicked`, che verrà creato nel passaggio successivo. La metà delle istanze di `Label` visualizza testo statico, mentre le istanze rimanenti usano il data binding alle proprietà di `WeatherData`. In fase di esecuzione, le istanze di `Label` che usano il data binding esamineranno le rispettive proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) per individuare l'oggetto `WeatherData` da usare nelle espressioni di binding. Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Inoltre, per [`Entry`](xref:Xamarin.Forms.Entry) viene specificato un nome con l'attributo `x:Name`. Ciò consente al file code-behind di accedere all'oggetto usando il nome assegnato.

1. In **Esplora soluzioni** espandere **MainPage.xaml** nel progetto **WebServiceTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    Il metodo `OnButtonClicked`, che viene eseguito al tocco su [`Button`](xref:Xamarin.Forms.Button), richiama il metodo `RestService.GetWeatherDataAsync` per recuperare i dati meteo per la città di cui è stato immesso il nome in [`Entry`](xref:Xamarin.Forms.Entry). Il metodo `GetWeatherDataAsync` richiede un argomento `string` che rappresenta l'URI dell'API Web richiamata e che viene generato dal metodo `GenerateRequestUri`. Questo metodo accetta l'indirizzo dell'endpoint archiviato nella costante `OpenWeatherMapEndpoint` e aggiunge i parametri di query che specificano:

    - La città per cui vengono richiesti i dati meteo.
    - Le unità di misura per la restituzione dei dati meteo.
    - La chiave API personale.

    Dopo aver recuperato i dati meteo richiesti, il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina viene impostato sull'oggetto `WeatherData`. Per altre informazioni sulla proprietà `BindingContext`, vedere [Binding con un contesto di binding](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) nella guida [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) viene ereditata tramite la struttura ad albero visuale. Pertanto, dato che è stata impostata per l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage), il valore viene ereditato dagli oggetti figlio di `ContentPage`, incluse le istanze di [`Label`](xref:Xamarin.Forms.Label).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. Toccare [`Button`](xref:Xamarin.Forms.Button) per recuperare i dati meteo correnti per Seattle:

    [![Screenshot dei dati meteo di Seattle, in iOS e Android](../images/consume-web-service.png "Dati meteo di Seattle")](../images/consume-web-service-large.png#lightbox "Dati meteo di Seattle")

    > [!IMPORTANT]
    > La chiave API OpenWeatherMap personale deve essere impostata come valore della costante `OpenWeatherMapAPIKey` nella classe `Constants`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su **MainPage.xaml** nel progetto **WebServiceTutorial** nel **riquadro della soluzione** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Entry`](xref:Xamarin.Forms.Entry), un elemento [`Button`](xref:Xamarin.Forms.Button) e una serie di istanze di [`Label`](xref:Xamarin.Forms.Label) in un elemento [`Grid`](xref:Xamarin.Forms.Grid). L'elemento `Entry` viene prepopolato con "Seattle" tramite l'impostazione della relativa proprietà [`Text`](xref:Xamarin.Forms.Entry.Text). `Button` imposta il relativo evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) su un gestore eventi denominato `OnButtonClicked`, che verrà creato nel passaggio successivo. La metà delle istanze di `Label` visualizza testo statico, mentre le istanze rimanenti usano il data binding alle proprietà di `WeatherData`. In fase di esecuzione, le istanze di `Label` che usano il data binding esamineranno le rispettive proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) per individuare l'oggetto `WeatherData` da usare nelle espressioni di binding. Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Inoltre, per [`Entry`](xref:Xamarin.Forms.Entry) viene specificato un nome con l'attributo `x:Name`. Ciò consente al file code-behind di accedere all'oggetto usando il nome assegnato.

    Per altre informazioni sull'utilizzo dei servizi Web basati su REST in Xamarin.Forms, vedere [Utilizzo di un servizio Web RESTful (guida)](~/xamarin-forms/data-cloud/consuming/rest.md).

1. Nel **riquadro della soluzione** espandere **MainPage.xaml** nel progetto **WebServiceTutorial** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    Il metodo `OnButtonClicked`, che viene eseguito al tocco su [`Button`](xref:Xamarin.Forms.Button), richiama il metodo `RestService.GetWeatherDataAsync` per recuperare i dati meteo per la città di cui è stato immesso il nome in [`Entry`](xref:Xamarin.Forms.Entry). Il metodo `GetWeatherDataAsync` richiede un argomento `string` che rappresenta l'URI dell'API Web richiamata e che viene generato dal metodo `GenerateRequestUri`. Questo metodo accetta l'indirizzo dell'endpoint archiviato nella costante `OpenWeatherMapEndpoint` e aggiunge i parametri di query che specificano:

    - La città per cui vengono richiesti i dati meteo.
    - Le unità di misura per la restituzione dei dati meteo.
    - La chiave API personale.

    Dopo aver recuperato i dati meteo richiesti, il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina viene impostato sull'oggetto `WeatherData`. Per altre informazioni sulla proprietà `BindingContext`, vedere [Binding con un contesto di binding](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) nella guida [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) viene ereditata tramite la struttura ad albero visuale. Pertanto, dato che è stata impostata per l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage), il valore viene ereditato dagli oggetti figlio di `ContentPage`, incluse le istanze di [`Label`](xref:Xamarin.Forms.Label).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. Toccare [`Button`](xref:Xamarin.Forms.Button) per recuperare i dati meteo correnti per Seattle:

    [![Screenshot dei dati meteo di Seattle, in iOS e Android](../images/consume-web-service.png "Dati meteo di Seattle")](../images/consume-web-service-large.png#lightbox "Dati meteo di Seattle")

    > [!IMPORTANT]
    > La chiave API OpenWeatherMap personale deve essere impostata come valore della costante `OpenWeatherMapAPIKey` nella classe `Constants`.

    Per altre informazioni sull'utilizzo dei servizi Web basati su REST in Xamarin.Forms, vedere [Utilizzo di un servizio Web RESTful (guida)](~/xamarin-forms/data-cloud/consuming/rest.md).
