---
title: Servizio Azure SignalR con xamarin. Forms
description: Introduzione a funzioni di Azure con xamarin. Forms e servizio Azure SignalR
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: 587f3dbbd46b65ec92c4e0eff18a56e89337f191
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658758"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Servizio Azure SignalR con xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureSignalR)

ASP.NET Core SignalR è un modello di applicazione che semplifica il processo di aggiunta di comunicazioni in tempo reale alle applicazioni. Azure SignalR Service consente lo sviluppo rapido e la distribuzione di applicazioni scalabili, SignalR. Funzioni di Azure sono i metodi di codice senza server e di breve durata che possono essere combinati a form basato su eventi applicazioni scalabili.

Questo articolo ed esempio illustrano come combinare servizio Azure SignalR e funzioni di Azure con xamarin. Forms, per recapitare i messaggi in tempo reale ai client connessi.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Creare un'App di funzioni di Azure e servizio Azure SignalR

L'applicazione di esempio è costituito da tre componenti principali: un hub di servizio Azure SignalR, un'istanza di funzioni di Azure con due funzioni e un'applicazione per dispositivi mobili che può inviare e ricevere messaggi. Questi componenti interagiscono come indicato di seguito:

1. L'applicazione per dispositivi mobili richiama una **Negotiate** funzioni di Azure per ottenere informazioni sull'hub di SignalR.
1. L'applicazione per dispositivi mobili Usa le informazioni di negoziazione per registrarsi con l'hub SignalR e costituisce una connessione.
1. Dopo la registrazione, l'applicazione per dispositivi mobili invia messaggi per il **parlare** funzioni di Azure.
1. Il **parlare** funzione passa il messaggio in arrivo nell'hub SignalR.
1. L'hub SignalR trasmette il messaggio a tutte le istanze di applicazione mobile connessa, tra cui il mittente originale.

> [!NOTE]
> Il **Negotiate** e **parlare** funzioni nell'applicazione di esempio possono essere eseguite in locale usando gli strumenti di runtime di Azure e Visual Studio 2019. Tuttavia, il servizio Azure SignalR non può essere emulato in locale ed è difficile espongono funzioni di Azure ospitate in locale da dispositivi fisici o virtuali per i test. È consigliabile distribuire funzioni di Azure a un'istanza di App per le funzioni di Azure, come in questo modo multi-piattaforma di test. Per informazioni dettagliate di distribuzione, vedere [distribuire funzioni di Azure con Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Creare un servizio Azure SignalR

Può essere creato un servizio Azure SignalR scegliendo **crea una risorsa** nell'angolo superiore sinistro del portale di Azure e la ricerca dei **SignalR**. Il servizio Azure SignalR possono essere creato al livello gratuito. Deve essere il servizio Azure SignalR **senza server** modalità del servizio. Se si sceglie accidentalmente il valore predefinito o la modalità classica del servizio, è possibile modificarlo in un secondo momento nelle proprietà del servizio Azure SignalR.

Lo screenshot seguente illustra la creazione di un nuovo servizio Azure SignalR:

![La creazione di schermata del servizio Azure SignalR nel portale di Azure](azure-signalr-images/azure-signalr-create.png "la creazione di un servizio Azure SignalR")

Una volta creato, il **tasti** sezione di un servizio Azure SignalR contiene un **stringa di connessione**, che viene usato per connettere l'App funzioni di Azure all'hub di SignalR. Lo screenshot seguente mostra dove trovare la stringa di connessione nel servizio Azure SignalR:

![Stringa di connessione SignalR di screenshot di Azure nel portale di Azure](azure-signalr-images/azure-signalr-connection-string.png "stringa di connessione di Azure SignalR")

Questa stringa di connessione viene usata per [distribuire funzioni di Azure con Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Creare un'App per le funzioni di Azure

Per testare l'applicazione di esempio, è necessario creare una nuova App funzioni di Azure nel portale di Azure. Annotare il **nome dell'App** come questo URL viene usato nell'applicazione di esempio **Constants.cs** file. Lo screenshot seguente illustra la creazione di una nuova App di funzioni di Azure denominato "xdocsfunctions":

[![Creazione di schermata dell'App per le funzioni Azure](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Funzioni di Azure possono essere distribuite in un'istanza di App per le funzioni di Azure da Visual Studio 2019. Le sezioni seguenti descrivono la distribuzione di due funzioni riportate nell'applicazione di esempio in un'istanza di App per le funzioni di Azure.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Compilare funzioni di Azure in Visual Studio 2019

L'applicazione di esempio contiene una libreria di classi chiamata **ChatServer**, che include due funzioni di Azure senza server in file chiamati **Negotiate.cs** e **Talk.cs**.

Il `Negotiate` funzione risponde alle richieste web con un `SignalRConnectionInfo` oggetto che contiene un' `AccessToken` proprietà e un `Url` proprietà. L'applicazione per dispositivi mobili Usa questi valori per registrare se stessa con l'hub SignalR. Il codice seguente illustra il `Negotiate` funzione:

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

Il `Talk` funzione risponde alle richieste HTTP POST che forniscono un oggetto di messaggio nel corpo della richiesta POST. Il corpo del POST viene trasformato in un `SignalRMessage` e inoltrata all'hub SignalR. Il codice seguente illustra il `Talk` funzione:

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

Per altre informazioni sulle funzioni di Azure e App di funzioni di Azure, vedere [documentazione di funzioni di Azure](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Distribuire funzioni di Azure con Visual Studio 2019

Visual Studio 2019 consente di distribuire le funzioni in un'App funzioni di Azure. Le funzioni ospitata in Azure semplificano il test multipiattaforma fornendo un endpoint accessibile da test per tutti i dispositivi.

L'app funzioni di esempio di pulsante destro del mouse e scegliendo **pubblica** avvia la finestra di dialogo per pubblicare funzioni per l'App funzioni di Azure. Dopo avere eseguito i passaggi precedenti per configurare un'App di funzione di Azure, è possibile scegliere **seleziona esistente** per pubblicare le applicazioni di esempio per l'App funzioni di Azure. Lo screenshot seguente mostra le opzioni di finestra di dialogo Pubblica in Visual Studio 2019:

![Finestra di dialogo Opzioni pubblicazione in Visual Studio 2019](azure-signalr-images/vs-publish-target.png "pubblicare le opzioni in Visual Studio 2019")

Dopo aver effettuato l'accesso al tuo account Microsoft, è possibile individuare e scegliere l'App funzioni di Azure come destinazione di pubblicazione. Lo screenshot seguente mostra un esempio di App per le funzioni di Azure nella finestra di dialogo pubblicazione 2019 di Visual Studio:

![Un'App funzioni di Azure nella finestra di dialogo pubblicazione Visual Studio 2019](azure-signalr-images/vs-app-selection.png "App funzioni di Azure nella finestra di dialogo pubblicazione di Visual Studio 2019")

Dopo aver selezionato un'App funzioni di Azure vengono visualizzati l'istanza, l'URL del sito, configurazione e altre informazioni sulla destinazione di App per le funzioni di Azure. Scegli **modificare le impostazioni di Azure App Service** e immettere la stringa di connessione nel **remoto** campo. La stringa di connessione è usata dal **Negotiate** e **parlare** funzioni che consentono di connettersi al servizio Azure SignalR ed è disponibile nel **chiavi** sezione di Azure SignalR Servizio nel portale di Azure. Per altre informazioni sulla stringa di connessione, vedere [creare un servizio Azure SignalR](#create-an-azure-signalr-service).

Dopo aver immesso la stringa di connessione, è possibile fare clic su **pubblica** per distribuire le funzioni per l'App funzioni di Azure. Una volta completata, conterrà le funzioni nell'App funzioni di Azure nel portale di Azure. Lo screenshot seguente mostra le funzioni pubblicate nel portale di Azure:

![Funzioni pubblicate nell'App funzioni di Azure](azure-signalr-images/azure-functions-deployed.png "funzioni pubblicate nell'App funzioni di Azure")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrare il servizio Azure SignalR con xamarin. Forms

L'integrazione tra l'applicazione xamarin. Forms e servizio Azure SignalR è una classe di servizio SignalR che viene creata un'istanza di `MainPage` classe con i gestori eventi assegnati a tre eventi. Per altre informazioni su questi gestori eventi, vedere [usare la classe di servizio SignalR in xamarin. Forms](#use-the-signalr-service-class-in-xamarinforms).

L'applicazione di esempio include un' **Constants.cs** classe che deve essere personalizzato con l'endpoint dell'URL dell'App funzioni di Azure. Impostare il valore della `HostName` proprietà all'indirizzo di App per le funzioni di Azure. Il codice seguente illustra il **Constants.cs** nelle proprietà con un esempio `HostName` valore:

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> Il `Username` proprietà nell'applicazione di esempio **Constants.cs** file Usa il dispositivo `RuntimePlatform` valore come nome utente. Questo rende facile per i dispositivi multipiattaforma di test e identificare la periferica che invia il messaggio. In un'applicazione reale, questo valore potrebbe essere un nome utente univoco, raccolti durante un segno di backup o accedere nel processo.

### <a name="the-signalr-service-class"></a>La classe di servizio SignalR

Il `SignalRService` classe la **ChatClient** progetto nell'applicazione di esempio viene illustrata un'implementazione che richiama le funzioni in un'App funzioni di Azure per connettersi a un servizio Azure SignalR.

Il `SendMessageAsync` metodo di `SignalRService` classe viene utilizzata per inviare messaggi al client connesso al servizio Azure SignalR. Questo metodo esegue una richiesta HTTP POST per il **parlare** ospitata nell'App funzioni di Azure, tra cui un serializzati con JSON (funzione) `Message` oggetto come payload POST. Il **parlare** funzione passa il messaggio al servizio Azure SignalR per trasmettere a connesse tutte le soluzioni client. Nel codice seguente viene illustrato il metodo `SendMessageAsync`.

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

Il `ConnectAsync` metodo nella `SignalRService` classe esegue una richiesta HTTP GET per il **Negotiate** funzione ospitata nell'App funzioni di Azure. Il **Negotiate** funzione restituisce JSON che viene deserializzato in un'istanza di `NegotiateInfo` classe. Una volta il `NegotiateInfo` oggetto viene recuperato, viene usato per registrare direttamente con il servizio Azure SignalR utilizzando un'istanza di `HubConnection` classe.

ASP.NET Core SignalR converte i dati in ingresso dalla connessione aperta in messaggi e consente agli sviluppatori di definire i tipi di messaggio e associare i gestori eventi ai messaggi in arrivo dal tipo. Il `ConnectAsync` metodo registra un gestore eventi per il nome del messaggio definito nell'applicazione di esempio **Constants.cs** file, ovvero "newMessage" per impostazione predefinita.

Nel codice seguente viene illustrato il metodo `ConnectAsync`.

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

Il `AddNewMessage` metodo viene associato come gestore dell'evento nel `ConnectAsync` del messaggio come illustrato nel codice precedente. Quando viene ricevuto un messaggio, il `AddNewMessage` metodo viene chiamato con i dati del messaggio specificati come un `JObject`. Il `AddNewMessage` metodo converte il `JObject` a un'istanza del `Message` classe e quindi richiama il gestore per `NewMessageReceived` se uno è stato associato. Nel codice seguente viene illustrato il metodo `AddNewMessage`.

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Usare la classe di servizio SignalR in xamarin. Forms

Che usano la classe di servizio SignalR in xamarin. Forms viene eseguita associando il `SignalRService` eventi in classi di `MainPage` classe code-behind.

Il `Connected` eventi nel `SignalRService` classe viene generata quando viene completata correttamente una connessione SignalR. Il `ConnectionFailed` eventi nel `SignalRService` classe viene generata quando una connessione SignalR non riesce. Il `SignalR_ConnectionChanged` metodo gestore dell'evento è associato a entrambi gli eventi nel `MainPage` costruttore. Questo gestore dell'evento aggiorna gli stati del pulsante connect e trasmissione in base alla connessione `success` argomento e aggiunge il messaggio fornito dall'evento alla chat coda usando il `AddMessage` (metodo). Il codice seguente illustra il `SignalR_ConnectionChanged` metodo del gestore eventi:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

Il `NewMessageReceived` eventi nel `SignalRService` classe viene generata quando un nuovo messaggio viene ricevuto dal servizio Azure SignalR. Il `SignalR_NewMessageReceived` metodo gestore dell'evento è associato ai `NewMessageReceived` evento nel `MainPage` costruttore. Questo gestore dell'evento converte in ingresso `Message` dell'oggetto in una stringa e lo aggiunge alla chat coda usando il `AddMessage` (metodo). Il codice seguente illustra il `SignalR_NewMessageReceived` metodo del gestore eventi:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

Il `AddMessage` metodo aggiunge un nuovo messaggio come un `Label` oggetto alla coda di chat. Il `AddMessage` viene spesso chiamato dai gestori di eventi all'esterno di thread dell'interfaccia utente principale, in modo che forza gli aggiornamenti dell'interfaccia utente si verifichi nel thread principale per evitare eccezioni. Nel codice seguente viene illustrato il metodo `AddMessage`.

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>Testare l'applicazione

L'applicazione di chat SignalR può essere testata nel iOS, Android e UWP purché:

1. Creare un servizio Azure SignalR.
1. Creare un'App per le funzioni di Azure.
1. Personalizzare il **Constants.cs** file con l'endpoint di App per le funzioni di Azure.

Una volta completati questi passaggi e l'applicazione viene eseguita, scegliere il **Connect** form pulsante una connessione con il servizio Azure SignalR. Immettere un messaggio, scegliere il **inviare** risultati pulsante nei messaggi visualizzati nella coda di chat su qualsiasi connessione applicazioni per dispositivi mobili.

## <a name="related-links"></a>Collegamenti correlati

* [Creazione di App per dispositivi mobili in tempo reale con Xamarin e SignalR](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introduzione a SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introduzione alle funzioni di Azure](/azure/azure-functions/functions-overview)
* [Documentazione di funzioni di Azure](/azure/azure-functions/)
* [Esempio di chat MVVM SignalR](https://github.com/lbugnion/sample-xamarin-signalr)
