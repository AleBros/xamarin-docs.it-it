---
ms.openlocfilehash: 3b1603b6af5ebb5558c3cd764f41fdbe24351b9b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "68669696"
---
Le richieste REST vengono effettuate tramite HTTP usando gli stessi verbi HTTP usati dai Web browser per recuperare le pagine e per inviare dati ai server. In questo esercizio si creerà una classe che usa il verbo GET per recuperare i dati dall'API Web [OpenWeatherMap](https://openweathermap.org/). Questa API Web può essere usata per recuperare i dati delle previsioni meteo per una località specificata. Per usare l'API Web, è necessario iscriversi per ottenere una chiave API.

> [!div class="nextstepaction"]
> [Iscriversi per ottenere la chiave API](https://home.openweathermap.org/users/sign_up)

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. In **Esplora soluzioni** aggiungere una nuova classe denominata **al progetto**WebServiceTutorial`Constants`. In **Constants.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public const string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Questo codice definisce due costanti. La costante `OpenWeatherMapEndpoint` definisce l'endpoint su cui verranno effettuate le richieste Web e la costante `OpenWeatherMapAPIKey` definisce la chiave API personale per il servizio [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > È necessario impostare la chiave API OpenWeatherMap personale come valore della costante `OpenWeatherMapAPIKey`.

1. In **Esplora soluzioni** aggiungere una nuova classe denominata **al progetto**WebServicesTutorial`WeatherData`. In **WeatherData.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Questo codice definisce quattro classi che vengono usate per modellare i dati JSON recuperati dal servizio Web. Ogni proprietà è decorata con un attributo `JsonProperty`, che contiene il nome di un campo JSON. Newtonsoft.Json userà questo mapping dei nomi dei campi JSON con le proprietà CLR durante la deserializzazione di dati JSON in oggetti del modello.

    > [!NOTE]
    > Le definizioni di classe precedenti sono state semplificate e non modellano completamente i dati JSON recuperati dal servizio Web. Per un esempio di modello di dati completo, vedere l'esempio [Weather App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/).

1. In **Esplora soluzioni** aggiungere una nuova classe denominata **al progetto**WebServiceTutorial`RestService`. In **RestService.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Questo codice definisce un singolo metodo, `GetWeatherDataAsync`, che recupera i dati meteo per una località specificata dall'API Web [OpenWeatherMap](https://openweathermap.org/). Questo metodo usa il metodo `HttpClient.GetAsync` per inviare una richiesta GET all'API Web specificata dall'argomento `uri`. L'API Web invia una risposta che viene archiviata in un oggetto `HttpResponseMessage`. La risposta include un codice di stato HTTP, che indica se la richiesta HTTP ha avuto esito positivo o negativo. A condizione che la richiesta abbia esito positivo, l'API Web risponde con codice di stato HTTP 200 (OK) e una risposta JSON, nella proprietà `HttpResponseMessage.Content`. I dati JSON vengono letti in una `string` usando il metodo `HttpContent.ReadAsStringAsync`, prima di essere deserializzati in un oggetto `WeatherData` con il metodo `JsonConvert.DeserializeObject`. Questo metodo usa i mapping tra i nomi dei campi JSON e le proprietà CLR, definite nella classe `WeatherData`, per eseguire la deserializzazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **riquadro della soluzione** aggiungere una nuova classe denominata **al progetto**WebServiceTutorial`Constants`. In **Constants.cs** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public static string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public static string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Questo codice definisce due costanti. La costante `OpenWeatherMapEndpoint` definisce l'endpoint su cui verranno effettuate le richieste Web e la costante `OpenWeatherMapAPIKey` definisce la chiave API personale per il servizio [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > È necessario impostare la chiave API OpenWeatherMap personale come valore della costante `OpenWeatherMapAPIKey`.

1. Nel **riquadro della soluzione** aggiungere una nuova classe denominata **al progetto**WebServicesTutorial`WeatherData`. In **WeatherData.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Questo codice definisce quattro classi che vengono usate per modellare i dati JSON recuperati dal servizio Web. Ogni proprietà è decorata con un attributo `JsonProperty`, che contiene il nome di un campo JSON. Newtonsoft.Json userà questo mapping dei nomi dei campi JSON con le proprietà CLR durante la deserializzazione di dati JSON in oggetti del modello.

    > [!NOTE]
    > Le definizioni di classe precedenti sono state semplificate e non modellano completamente i dati JSON recuperati dal servizio Web. Per un esempio di modello di dati completo, vedere l'esempio [Weather App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/).

1. Nel **riquadro della soluzione** aggiungere una nuova classe denominata **al progetto**WebServiceTutorial`RestService`. In **RestService.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Questo codice definisce un singolo metodo, `GetWeatherDataAsync`, che recupera i dati meteo per una località specificata dall'API Web [OpenWeatherMap](https://openweathermap.org/). Questo metodo usa il metodo `HttpClient.GetAsync` per inviare una richiesta GET all'API Web specificata dall'argomento `uri`. L'API Web invia una risposta che viene archiviata in un oggetto `HttpResponseMessage`. La risposta include un codice di stato HTTP, che indica se la richiesta HTTP ha avuto esito positivo o negativo. A condizione che la richiesta abbia esito positivo, l'API Web risponde con codice di stato HTTP 200 (OK) e una risposta JSON, nella proprietà `HttpResponseMessage.Content`. I dati JSON vengono letti in una `string` usando il metodo `HttpContent.ReadAsStringAsync`, prima di essere deserializzati in un oggetto `WeatherData` con il metodo `JsonConvert.DeserializeObject`. Questo metodo usa i mapping tra i nomi dei campi JSON e le proprietà CLR, definite nella classe `WeatherData`, per eseguire la deserializzazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.
