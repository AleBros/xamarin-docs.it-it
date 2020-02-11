---
title: Autenticare un servizio Web RESTful
description: L'autenticazione di base consente di accedere alle risorse solo ai client con le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio Web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 23516603633116a8e28ae33004bdb6fc8764ec21
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032817"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticare un servizio Web RESTful

_HTTP supporta l'utilizzo di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base consente di accedere alle risorse solo ai client con le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio Web RESTful._

> [!NOTE]
> In iOS 9 e versioni successive, la sicurezza del trasporto app impone connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo così la divulgazione accidentale di informazioni riservate. Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9, tutte le connessioni saranno soggette ai requisiti di sicurezza di ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.
> È possibile escludere ATS da se non è possibile usare il protocollo `HTTPS` e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticazione degli utenti tramite HTTP

L'autenticazione di base è il meccanismo di autenticazione più semplice supportato da HTTP e implica che il client invii il nome utente e la password come testo con codifica Base64 non crittografato. Funziona nel modo seguente:

- Se un servizio Web riceve una richiesta per una risorsa protetta, rifiuta la richiesta con un codice di stato HTTP 401 (accesso negato) e imposta l'intestazione della risposta WWW-Authenticate, come illustrato nel diagramma seguente:

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Se un servizio Web riceve una richiesta per una risorsa protetta, con l'intestazione `Authorization` impostata correttamente, il servizio Web risponde con un codice di stato HTTP 200, che indica che la richiesta è riuscita e che le informazioni richieste sono nella risposta. Questo scenario è illustrato nel diagramma seguente:

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> L'autenticazione di base deve essere utilizzata solo su una connessione HTTPS. Se utilizzata su una connessione HTTP, l'intestazione `Authorization` può essere facilmente decodificata se il traffico HTTP viene acquisito da un utente malintenzionato.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Specifica dell'autenticazione di base in una richiesta Web

L'uso dell'autenticazione di base viene specificato come indicato di seguito:

1. La stringa "Basic" viene aggiunta all'intestazione `Authorization` della richiesta.
1. Il nome utente e la password vengono combinati in una stringa con il formato "username: password", che è quindi codificato in base 64 e aggiunto all'intestazione `Authorization` della richiesta.

Pertanto, con il nome utente "XamarinUser" e la password "XamarinPassword", l'intestazione diventa:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La classe `HttpClient` può impostare il valore dell'intestazione `Authorization` nella proprietà `HttpClient.DefaultRequestHeaders.Authorization`. Poiché l'istanza di `HttpClient` esiste tra più richieste, è necessario impostare l'intestazione `Authorization` solo una volta, anziché quando si effettua ogni richiesta, come illustrato nell'esempio di codice seguente:

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

Quindi, quando viene effettuata una richiesta a un'operazione del servizio Web, la richiesta viene firmata con l'intestazione `Authorization`, che indica se l'utente dispone dell'autorizzazione per richiamare l'operazione.

> [!NOTE]
> Sebbene questo codice memorizzi le credenziali come costanti, non devono essere archiviate in un formato non sicuro in un'applicazione pubblicata. Il NuGet [Xamarith. auth](https://www.nuget.org/packages/Xamarin.Auth/) fornisce funzionalità per l'archiviazione sicura delle credenziali. Per altre informazioni [, vedere archiviazione e recupero di informazioni sull'account nei dispositivi](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="processing-the-authorization-header-server-side"></a>Elaborazione del lato server dell'intestazione Authorization

Il servizio REST deve decorare ogni azione con l'attributo `[BasicAuthentication]`. Questo attributo viene usato per analizzare l'intestazione `Authorization` e determinare se le credenziali con codifica Base64 sono valide confrontando tali credenziali con i valori archiviati nel *file Web. config*. Sebbene questo approccio sia appropriato per un servizio di esempio, è necessario estendere per un servizio Web pubblico.

Nel modulo di autenticazione di base usato da IIS, gli utenti vengono autenticati con le credenziali di Windows. È pertanto necessario che gli utenti dispongano di account nel dominio del server. Tuttavia, il modello di autenticazione di base può essere configurato in modo da consentire l'autenticazione personalizzata, in cui gli account utente vengono autenticati su un'origine esterna, ad esempio un database. Per altre informazioni, vedere [autenticazione di base in API Web ASP.NET](https://www.asp.net/web-api/overview/security/basic-authentication) nel sito Web ASP.NET.

> [!NOTE]
> L'autenticazione di base non è stata progettata per gestire la disconnessione. Pertanto, l'approccio di autenticazione standard di base per la disconnessione consiste nel terminare la sessione.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
