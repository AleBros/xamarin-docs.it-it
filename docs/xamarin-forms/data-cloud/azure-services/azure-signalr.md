---
title: Servizio Azure SignalR con Novell. Forms
description: Introduzione al servizio Azure SignalR e alle funzioni di Azure con Novell. Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: e95dd72513562bba9fb513c4742e476bc7be0c94
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487412"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Servizio Azure SignalR con Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core SignalR è un modello di applicazione che semplifica il processo di aggiunta della comunicazione in tempo reale alle applicazioni. Il servizio Azure SignalR consente uno sviluppo e una distribuzione rapidi di applicazioni SignalR scalabili. Funzioni di Azure è un metodo di codice senza server di breve durata che può essere combinato per formare applicazioni scalabili e guidate dagli eventi.

Questo articolo ed esempio illustra come combinare il servizio Azure SignalR e funzioni di Azure con Novell. Forms, per fornire messaggi in tempo reale ai client connessi.

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Creare un servizio Azure SignalR e l'app funzioni di Azure

L'applicazione di esempio è costituita da tre componenti principali: un hub del servizio Azure SignalR, un'istanza di funzioni di Azure con due funzioni e un'applicazione per dispositivi mobili che può inviare e ricevere messaggi. Questi componenti interagiscono nel modo seguente:

1. L'applicazione per dispositivi mobili richiama una funzione di Azure **Negotiate** per ottenere informazioni sull'hub SignalR.
1. L'applicazione per dispositivi mobili usa le informazioni di negoziazione per la registrazione con l'hub SignalR e costituisce una connessione.
1. Dopo la registrazione, l'applicazione per dispositivi mobili invia messaggi alla funzione di Azure **Talk** .
1. La funzione **Talk** passa il messaggio in arrivo all'hub SignalR.
1. L'hub SignalR trasmette il messaggio a tutte le istanze dell'applicazione mobile connesse, incluso il mittente originale.

> [!IMPORTANT]
> Le funzioni **Negotiate** e **Talk** nell'applicazione di esempio possono essere eseguite localmente con Visual Studio 2019 e gli strumenti di runtime di Azure. Il servizio Azure SignalR, tuttavia, non può essere emulato localmente ed è difficile esporre funzioni di Azure ospitate localmente a dispositivi fisici o virtuali per il test. Si consiglia di distribuire le funzioni di Azure in un'istanza di app di funzioni di Azure, in quanto consente il testing multipiattaforma. Per informazioni dettagliate sulla distribuzione, vedere [distribuire funzioni di Azure con Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Creare un servizio Azure SignalR

È possibile creare un servizio Azure SignalR scegliendo **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure e cercando **SignalR**. Il servizio Azure SignalR può essere creato a livello gratuito. Il servizio Azure SignalR deve essere in modalità servizio senza **Server** . Se si sceglie accidentalmente la modalità del servizio predefinita o classica, è possibile modificarla in un secondo momento nelle proprietà del servizio Azure SignalR.

Lo screenshot seguente illustra la creazione di un nuovo servizio Azure SignalR:

![Screenshot della creazione del servizio Azure SignalR nella portale di Azure](azure-signalr-images/azure-signalr-create.png "Creazione di un servizio Azure SignalR")

Una volta creata, la sezione **chiavi** di un servizio Azure SignalR contiene una **stringa di connessione**, che viene usata per connettere l'app funzioni di Azure all'hub SignalR. Lo screenshot seguente mostra dove trovare la stringa di connessione nel servizio Azure SignalR:

![Screenshot della stringa di connessione di Azure SignalR nella portale di Azure](azure-signalr-images/azure-signalr-connection-string.png "Stringa di connessione di Azure SignalR")

Questa stringa di connessione viene usata per [distribuire funzioni di Azure con Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Creare un'app funzioni di Azure

Per testare l'applicazione di esempio, è necessario creare una nuova app funzioni di Azure nel portale di Azure. Prendere nota del nome dell' **app** perché questo URL viene usato nel file **Constants.cs** dell'applicazione di esempio. Lo screenshot seguente illustra la creazione di una nuova app funzioni di Azure denominata "xdocsfunctions":

[![screenshot della creazione di app di funzioni di Azure](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Funzioni di Azure può essere distribuito in un'istanza di app di funzioni di Azure da Visual Studio 2019. Le sezioni seguenti descrivono la distribuzione di due funzioni nell'applicazione di esempio in un'istanza di app di funzioni di Azure.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Compilare funzioni di Azure in Visual Studio 2019

L'applicazione di esempio contiene una libreria di classi denominata **ChatServer**, che include due funzioni di Azure senza server nei file denominati **Negotiate.cs** e **Talk.cs**.

La funzione `Negotiate` risponde alle richieste Web con un oggetto `SignalRConnectionInfo` contenente una proprietà `AccessToken` e una proprietà `Url`. L'applicazione per dispositivi mobili usa questi valori per registrarsi con l'hub SignalR. Il codice seguente illustra la funzione `Negotiate`:

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

La funzione `Talk` risponde alle richieste HTTP POST che forniscono un oggetto messaggio nel corpo del POST. Il corpo del POST viene trasformato in un `SignalRMessage` e inoltrato all'hub SignalR. Il codice seguente illustra la funzione `Talk`:

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

Per altre informazioni sulle funzioni di Azure e sulle app per funzioni di Azure, vedere la [documentazione di funzioni](/azure/azure-functions/)di Azure.

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Distribuire funzioni di Azure con Visual Studio 2019

Visual Studio 2019 consente di distribuire le funzioni in un'app funzioni di Azure. Le funzioni ospitate in Azure semplificano i test multipiattaforma fornendo un endpoint di test accessibile per tutti i dispositivi.

Facendo clic con il pulsante destro del mouse sull'app funzioni di esempio e scegliendo **pubblica** viene avviata la finestra di dialogo per pubblicare funzioni nell'app funzioni Se è stata seguita la procedura precedente per configurare un app per le funzioni di Azure, è possibile scegliere **selezionare esistente** per pubblicare le applicazioni di esempio nell'app funzioni di Azure. La schermata seguente mostra le opzioni della finestra di dialogo pubblica in Visual Studio 2019:

![Finestra di dialogo pubblica scelte in Visual Studio 2019](azure-signalr-images/vs-publish-target.png "Opzioni di pubblicazione in Visual Studio 2019")

Dopo aver eseguito l'accesso alla account Microsoft, è possibile individuare e scegliere l'app funzioni di Azure come destinazione di pubblicazione. La schermata seguente illustra un'app funzioni di Azure di esempio nella finestra di dialogo di pubblicazione di Visual Studio 2019:

![App funzioni di Azure nella finestra di dialogo di pubblicazione di Visual Studio 2019](azure-signalr-images/vs-app-selection.png "Finestra di dialogo di pubblicazione di app funzioni di Azure in Visual Studio 2019")

Dopo aver selezionato un'istanza dell'app funzioni di Azure, vengono visualizzati l'URL del sito, la configurazione e altre informazioni sull'app funzioni di Azure di destinazione. Scegliere **Modifica impostazioni del servizio app Azure** e immettere la stringa di connessione nel campo **remoto** . La stringa di connessione viene usata dalle funzioni **Negotiate** e **Talk** per connettersi al servizio Azure SignalR ed è disponibile nella sezione **chiavi** del servizio Azure SignalR nel portale di Azure. Per altre informazioni sulla stringa di connessione, vedere [creare un servizio Azure SignalR](#create-an-azure-signalr-service).

Dopo aver immesso la stringa di connessione, è possibile fare clic su **pubblica** per distribuire le funzioni nell'app funzioni di Azure. Al termine, le funzioni verranno elencate nell'app funzioni di Azure nell'portale di Azure. Lo screenshot seguente mostra le funzioni pubblicate nel portale di Azure:

![Funzioni pubblicate nell'app funzioni di Azure](azure-signalr-images/azure-functions-deployed.png "Funzioni pubblicate nell'app funzioni di Azure")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrare il servizio Azure SignalR con Novell. Forms

L'integrazione tra il servizio Azure SignalR e l'applicazione Novell. Forms è una classe del servizio SignalR di cui viene creata un'istanza nella classe `MainPage` con i gestori eventi assegnati a tre eventi. Per altre informazioni su questi gestori eventi, vedere [usare la classe del servizio SignalR in Novell. Forms](#use-the-signalr-service-class-in-xamarinforms).

L'applicazione di esempio include una classe **Constants.cs** che deve essere personalizzata con l'endpoint URL dell'app funzioni di Azure. Impostare il valore della proprietà `HostName` sull'indirizzo dell'app funzioni di Azure. Nel codice seguente vengono illustrate le proprietà **Constants.cs** con un valore `HostName` di esempio:

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
> La proprietà `Username` nel file **Constants.cs** dell'applicazione di esempio usa il valore di `RuntimePlatform` del dispositivo come nome utente. Questo consente di testare facilmente i dispositivi multipiattaforma e identificare il dispositivo che invia il messaggio. In un'applicazione reale, questo valore sarebbe probabilmente un nome utente univoco, raccolto durante un processo di iscrizione o accesso.

### <a name="the-signalr-service-class"></a>Classe del servizio SignalR

La classe `SignalRService` nel progetto **chatclient** nell'applicazione di esempio illustra un'implementazione che richiama le funzioni in un'app funzioni di Azure per connettersi a un servizio Azure SignalR.

Il metodo `SendMessageAsync` nella classe `SignalRService` viene usato per inviare messaggi ai client connessi al servizio Azure SignalR. Questo metodo esegue una richiesta HTTP POST alla funzione **Talk** ospitata nell'app funzioni di Azure, incluso un oggetto `Message` serializzato in JSON come payload post. La funzione **Talk** passa il messaggio al servizio Azure SignalR per la trasmissione a tutti i client connessi. Nel codice seguente viene illustrato il metodo `SendMessageAsync`.

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

Il metodo `ConnectAsync` nella classe `SignalRService` esegue una richiesta HTTP GET alla funzione **Negotiate** ospitata nell'app funzioni di Azure. La funzione **Negotiate** restituisce JSON deserializzato in un'istanza della classe `NegotiateInfo`. Una volta recuperato l'oggetto `NegotiateInfo`, questo viene usato per la registrazione diretta con il servizio Azure SignalR usando un'istanza della classe `HubConnection`.

ASP.NET Core SignalR converte i dati in ingresso dalla connessione aperta ai messaggi e consente agli sviluppatori di definire i tipi di messaggio e di associare i gestori eventi ai messaggi in ingresso per tipo. Il metodo `ConnectAsync` registra un gestore eventi per il nome del messaggio definito nel file **Constants.cs** dell'applicazione di esempio, che per impostazione predefinita è "newMessage".

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

Il metodo `AddNewMessage` viene associato come gestore eventi nel messaggio di `ConnectAsync`, come illustrato nel codice precedente. Quando viene ricevuto un messaggio, viene chiamato il metodo `AddNewMessage` con i dati del messaggio forniti come `JObject`. Il metodo `AddNewMessage` converte la `JObject` in un'istanza della classe `Message` e quindi richiama il gestore per `NewMessageReceived` se ne è stato associato uno. Nel codice seguente viene illustrato il metodo `AddNewMessage`.

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Usare la classe del servizio SignalR in Novell. Forms

L'uso della classe del servizio SignalR in Novell. Forms viene eseguito associando gli eventi della classe `SignalRService` nella classe code-behind `MainPage`.

L'evento `Connected` nella classe `SignalRService` viene generato quando una connessione SignalR viene completata correttamente. L'evento `ConnectionFailed` nella classe `SignalRService` viene generato in caso di errore di una connessione SignalR. Il metodo del gestore eventi `SignalR_ConnectionChanged` è associato a entrambi gli eventi nel costruttore di `MainPage`. Questo gestore eventi aggiorna gli Stati dei pulsanti Connetti e invia in base all'argomento `success` di connessione e aggiunge il messaggio fornito dall'evento alla coda della chat utilizzando il metodo `AddMessage`. Il codice seguente illustra il metodo del gestore dell'evento `SignalR_ConnectionChanged`:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

L'evento `NewMessageReceived` nella classe `SignalRService` viene generato quando viene ricevuto un nuovo messaggio dal servizio Azure SignalR. Il metodo del gestore eventi `SignalR_NewMessageReceived` è associato all'evento `NewMessageReceived` nel costruttore `MainPage`. Questo gestore eventi converte l'oggetto `Message` in ingresso in una stringa e lo aggiunge alla coda della chat usando il metodo `AddMessage`. Il codice seguente illustra il metodo del gestore dell'evento `SignalR_NewMessageReceived`:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

Il metodo `AddMessage` aggiunge un nuovo messaggio come oggetto `Label` alla coda della chat. Il metodo `AddMessage` viene spesso chiamato dai gestori eventi dall'esterno del thread principale dell'interfaccia utente, quindi forza gli aggiornamenti dell'interfaccia utente nel thread principale per impedire le eccezioni. Nel codice seguente viene illustrato il metodo `AddMessage`.

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

L'applicazione di chat SignalR può essere testata in iOS, Android e UWP purché siano disponibili:

1. Creazione di un servizio Azure SignalR.
1. È stata creata un'app funzioni di Azure.
1. Personalizzare il file **Constants.cs** con l'endpoint dell'app funzioni di Azure.

Dopo aver completato questi passaggi e aver eseguito l'applicazione, facendo clic sul pulsante **Connetti** si crea una connessione con il servizio Azure SignalR. Se si digita un messaggio e si fa clic sul pulsante **Invia** , i messaggi vengono visualizzati nella coda della chat in qualsiasi applicazione mobile connessa.

## <a name="related-links"></a>Collegamenti correlati

* [Creazione di app per dispositivi mobili in tempo reale con Novell e SignalR](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introduzione a SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introduzione alle funzioni di Azure](/azure/azure-functions/functions-overview)
* [Documentazione di Funzioni di Azure](/azure/azure-functions/)
* [Esempio di chat SignalR MVVM](https://github.com/lbugnion/sample-xamarin-signalr)
