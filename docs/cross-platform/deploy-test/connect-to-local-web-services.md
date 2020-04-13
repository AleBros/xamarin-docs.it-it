---
title: Connettersi a servizi Web locali da simulatori iOS ed emulatori Android
description: Questo articolo spiega come un'applicazione per dispositivi mobili Xamarin in esecuzione nel simulatore iOS o nell'emulatore Android può utilizzare un servizio Web ASP.NET Core in esecuzione in locale.
ms.prod: xamarin
ms.assetid: FD8FE199-898B-4841-8041-CC9CA1A00917
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
ms.openlocfilehash: 29261f2ef6366c0dac8ac82e63584366a5cca0b0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135277"
---
# <a name="connect-to-local-web-services-from-ios-simulators-and-android-emulators"></a>Connettersi a servizi Web locali da simulatori iOS ed emulatori Android

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/)

Molte applicazioni per dispositivi mobili utilizzano servizi Web. Durante la fase di sviluppo, è comune distribuire un servizio Web in locale e utilizzarlo da un'applicazione per dispositivi mobili in esecuzione nel simulatore iOS o nell'emulatore Android. Questo evita di dover distribuire il servizio Web a un endpoint ospitato e supporta una semplice esperienza di debug perché sia l'applicazione per dispositivi mobili che il servizio Web sono in esecuzione in locale.

Le applicazioni per dispositivi mobili in esecuzione nel simulatore iOS o nell'emulatore Android possono utilizzare servizi Web ASP.NET Core in esecuzione in locale ed esposti tramite HTTP, come indicato di seguito:

- Le applicazioni in esecuzione nel simulatore iOS possono connettersi ai servizi Web HTTP locali tramite l'indirizzo IP del computer o tramite il nome host `localhost`. Ad esempio, dato un servizio Web HTTP locale che espone un'operazione GET tramite l'URI relativo `/api/todoitems/`, un'applicazione in esecuzione nel simulatore iOS può utilizzare l'operazione inviando una richiesta GET a `http://localhost:<port>/api/todoitems/`.
- Le applicazioni in esecuzione nell'emulatore Android possono connettersi ai servizi Web HTTP locali tramite l'indirizzo `10.0.2.2`, ovvero un alias per l'interfaccia di loopback host (`127.0.0.1` nel computer di sviluppo). Ad esempio, dato un servizio Web HTTP locale che espone un'operazione GET tramite l'URI relativo `/api/todoitems/`, un'applicazione in esecuzione nell'emulatore Android può utilizzare l'operazione inviando una richiesta GET a `http://10.0.2.2:<port>/api/todoitems/`.

Sono necessari tuttavia altri interventi, affinché un'applicazione in esecuzione nel simulatore iOS o nell'emulatore Android possa utilizzare un servizio Web locale esposto tramite HTTPS. Per questo scenario, il processo è il seguente:

1. Creare un certificato di sviluppo autofirmato nel computer. Per altre informazioni, vedere [Creare un certificato di sviluppo](#create-a-development-certificate).
1. Configurare il progetto `HttpClient` per utilizzare lo stack di rete appropriato per la build di debug. Per altre informazioni, vedere [Configurare il progetto](#configure-your-project).
1. Specificare l'indirizzo del computer locale. Per altre informazioni, vedere [Specificare l'indirizzo del computer locale](#specify-the-local-machine-address).
1. Evitare il controllo di sicurezza del certificato di sviluppo locale. Per altre informazioni, vedere [Evitare il controllo di sicurezza del certificato](#bypass-the-certificate-security-check).

Ogni elemento verrà presentato singolarmente.

## <a name="create-a-development-certificate"></a>Creare un certificato di sviluppo

L'installazione di .NET Core SDK include l'installazione del certificato di sviluppo HTTPS ASP.NET Core nell'archivio certificati utente locale. Tuttavia, anche se il certificato viene installato, non è attendibile. Per rendere attendibile il certificato, seguire questa procedura una sola volta per eseguire lo strumento `dev-certs` dotnet:

```dotnetcli
dotnet dev-certs https --trust
```

Il comando seguente consente di visualizzare informazioni della Guida sullo strumento `dev-certs`:

```dotnetcli
dotnet dev-certs https --help
```

In alternativa, quando si esegue un progetto ASP.NET Core 2.1 (o versione successiva), che usa HTTPS, Visual Studio rileva se il certificato di sviluppo è mancante e offre di installarlo e impostarlo come attendibile.

> [!NOTE]
> Il certificato di sviluppo HTTPS ASP.NET Core è autofirmato.

Per altre informazioni sull'abilitazione di HTTPS locale nel computer, vedere [Abilitare HTTPS locale](/aspnet/core/getting-started#enable-local-https).

## <a name="configure-your-project"></a>Configurare il progetto

Le applicazioni Xamarin in esecuzione in iOS e Android possono specificare quale stack di rete viene usato dalla classe `HttpClient`, scegliendo tra uno stack di rete gestito o stack di rete nativi. Lo stack gestito offre un alto elevato livello di compatibilità con il codice .NET esistente, ma è limitato a TLS 1.0 e può risultare più lento e comportare un aumento delle dimensioni del file eseguibile. Gli stack nativi possono essere più veloci e offrono maggiore sicurezza, ma potrebbero non fornire tutte le funzionalità della classe `HttpClient`.

### <a name="ios"></a>iOS

Le applicazioni Xamarin in esecuzione in iOS possono usare lo stack di rete gestito oppure gli stack di rete `CFNetwork` o `NSUrlSession` nativi. Per impostazione predefinita, i nuovi progetti per la piattaforma iOS usano lo stack di rete `NSUrlSession` per supportare TLS 1.2 e usano le API native per migliorare le prestazioni e ottenere dimensioni dei file eseguibili più piccole.

Tuttavia, quando un'applicazione deve connettersi a un servizio Web protetto in esecuzione in locale, per test di sviluppo, è più facile usare lo stack di rete gestito. Di conseguenza, è consigliabile impostare profili del simulatore per build di debug per usare lo stack di rete gestito e profili per build di versione per usare lo stack di rete `NSUrlSession`. Ogni stack di rete può essere impostato a livello di codice o tramite un selettore nelle opzioni del progetto. Per altre informazioni, vedere [HttpClient e selettore dell'implementazione di SSL/TLS per iOS/macOS](~/cross-platform/macios/http-stack.md).

### <a name="android"></a>Android

Le applicazioni Xamarin in esecuzione in Android possono usare lo stack di rete gestito `HttpClient` oppure lo stack di rete `AndroidClientHandler` nativo. Per impostazione predefinita, i nuovi progetti per la piattaforma Android usano lo stack di rete `AndroidClientHandler` per supportare TLS 1.2 e usano le API native per migliorare le prestazioni e ottenere dimensioni dei file eseguibili più piccole. Per ulteriori informazioni sugli stack di rete Android, vedere Selettore di implementazione [HttpClient Stack e SSL/TLS per Android.](~/android/app-fundamentals/http-stack.md)

## <a name="specify-the-local-machine-address"></a>Specificare l'indirizzo del computer locale

Il simulatore iOS e l'emulatore Android consentono entrambi l'accesso ai servizi Web sicuri in esecuzione nel computer locale. Tuttavia, l'indirizzo del computer locale è diverso per ognuno.

### <a name="ios"></a>iOS

Il simulatore iOS usa la rete del computer host. Le applicazioni in esecuzione nel simulatore possono quindi connettersi ai servizi Web in esecuzione nel computer locale tramite l'indirizzo IP del computer o tramite il nome host `localhost`. Ad esempio, dato un servizio Web sicuro locale che espone un'operazione GET tramite l'URI relativo `/api/todoitems/`, un'applicazione in esecuzione nel simulatore iOS può utilizzare l'operazione inviando una richiesta GET a `https://localhost:<port>/api/todoitems/`.

> [!NOTE]
> Quando si esegue un'applicazione per dispositivi mobili nel simulatore iOS da Windows, l'applicazione viene visualizzata nel [simulatore iOS remoto per Windows](~/tools/ios-simulator/index.md). Tuttavia, l'applicazione è in esecuzione nel Mac associato. Pertanto, non vi è alcun accesso tramite localhost a un servizio Web in esecuzione in Windows per un'applicazione iOS in esecuzione in un Mac.

### <a name="android"></a>Android

Ogni istanza dell'emulatore di Android è isolata dalle interfacce di rete del computer di sviluppo e viene eseguita dietro un router virtuale. Pertanto, un dispositivo emulato non può visualizzare il computer di sviluppo o altre istanze dell'emulatore in rete.

Tuttavia, il router virtuale per ogni emulatore gestisce uno spazio di rete speciale che include gli indirizzi già allocati, con l'indirizzo `10.0.2.2` usato come alias per l'interfaccia di loopback host (127.0.0.1 nel computer di sviluppo). Pertanto, dato un servizio Web sicuro locale che espone un'operazione GET tramite l'URI relativo `/api/todoitems/`, un'applicazione in esecuzione nell'emulatore Android può utilizzare l'operazione inviando una richiesta GET a `https://10.0.2.2:<port>/api/todoitems/`.

### <a name="xamarinforms-example"></a>Esempio di Xamarin.Forms

In un'applicazione Xamarin.Forms, la [`Device`](xref:Xamarin.Forms.Device) classe può essere utilizzata per rilevare la piattaforma su cui è in esecuzione l'applicazione. Il nome host appropriato, che consente l'accesso ai servizi Web sicuri locali, può quindi essere impostato come segue:

```csharp
public static string BaseAddress =
    Device.RuntimePlatform == Device.Android ? "https://10.0.2.2:5001" : "https://localhost:5001";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

## <a name="bypass-the-certificate-security-check"></a>Evitare il controllo di sicurezza del certificato

Il tentativo di richiamare un servizio Web sicuro locale da un'applicazione in esecuzione nel simulatore iOS o nell'emulatore Android comporterà la generazione di una `HttpRequestException`, anche quando si usa lo stack di rete gestito in ogni piattaforma. Questo avviene perché il certificato di sviluppo HTTPS locale è autofirmato e i certificati autofirmati non sono considerati attendibili da iOS o Android.

Pertanto, è necessario ignorare gli errori SSL quando un'applicazione utilizza un servizio Web sicuro locale. Il meccanismo per eseguire questa operazione è attualmente diverso su iOS e Android.

### <a name="ios"></a>iOS

Gli errori SSL possono essere ignorati in iOS per i servizi `ServicePointManager.ServerCertificateValidationCallback` Web protetti locali, quando si usa lo stack di rete gestito, impostando la proprietà su un callback che ignora il risultato del controllo di sicurezza del certificato per il certificato di sviluppo HTTPS locale:

```csharp
#if DEBUG
    System.Net.ServicePointManager.ServerCertificateValidationCallback += (sender, certificate, chain, sslPolicyErrors) =>
    {
        if (certificate.Issuer.Equals("CN=localhost"))
            return true;
        return sslPolicyErrors == System.Net.Security.SslPolicyErrors.None;
    };
#endif
```

In questo esempio di codice, viene restituito il risultato della convalida del certificato server quando il certificato sottoposto a convalida non è il certificato `localhost`. Per questo certificato, il risultato della convalida viene ignorato e viene restituito il valore `true`, che indica che il certificato è valido. Questo codice deve essere `AppDelegate.FinishedLaunching` aggiunto al metodo in `LoadApplication(new App())` iOS, prima della chiamata al metodo.

> [!NOTE]
> Gli stack di rete nativi in iOS `ServerCertificateValidationCallback`non si collegano al file .

### <a name="android"></a>Android

Gli errori SSL possono essere ignorati in Android per i `AndroidClientHandler` servizi Web protetti `ServerCertificateCustomValidationCallback` locali, `HttpClientHandler` quando si utilizzano gli stack di rete gestiti e nativi, impostando la proprietà su un oggetto su un callback che ignora il risultato del controllo di sicurezza del certificato per il certificato di sviluppo HTTPS locale:

```csharp
public HttpClientHandler GetInsecureHandler()
{
    var handler = new HttpClientHandler();
    handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) =>
    {
        if (cert.Issuer.Equals("CN=localhost"))
            return true;
        return errors == System.Net.Security.SslPolicyErrors.None;
    };
    return handler;
}
```

In questo esempio di codice, viene restituito il risultato della convalida del certificato server quando il certificato sottoposto a convalida non è il certificato `localhost`. Per questo certificato, il risultato della convalida viene ignorato e viene restituito il valore `true`, che indica che il certificato è valido. L'oggetto risultante `HttpClientHandler` deve essere `HttpClient` passato come argomento al costruttore.

## <a name="related-links"></a>Collegamenti correlati

- [TodoREST (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/)
- [Abilitare HTTPS locale](/aspnet/core/getting-started#enable-local-https)
- [HttpClient e selettore dell'implementazione di SSL/TLS per iOS o macOS](~/cross-platform/macios/http-stack.md)
- [Stack HttpClient e selettore dell'implementazione di SSL/TLS per Android](~/android/app-fundamentals/http-stack.md)
