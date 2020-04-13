---
title: Autenticare un servizio Web RESTful
description: L'autenticazione di base fornisce l'accesso alle risorse solo ai client che dispongono delle credenziali corrette. In questo articolo viene illustrato come utilizzare l'autenticazione di base per proteggere l'accesso alle risorse del servizio Web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 65606b72c3944809a09b8c70b9cdbb5524e0f856
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388590"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticare un servizio Web RESTful

_HTTP supporta l'utilizzo di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse solo ai client che dispongono delle credenziali corrette. In questo articolo viene illustrato come utilizzare l'autenticazione di base per proteggere l'accesso alle risorse del servizio Web RESTful.This article demonstrates how to use basic authentication to protect access to RESTful web service resources._

> [!NOTE]
> In iOS 9 e versione successiva, App Transport Security (ATS) impone connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo in tal modo la divulgazione accidentale di informazioni riservate. Poiché ATS è abilitato per impostazione predefinita nelle app create per iOS 9, tutte le connessioni saranno soggette ai requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.
> ATS può essere escluso se non è `HTTPS` possibile utilizzare il protocollo e la comunicazione sicura per le risorse Internet. Ciò può essere ottenuto aggiornando il file **Info.plist** dell'app. Per ulteriori informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticazione degli utenti su HTTPAuthenticating Users over HTTP

L'autenticazione di base è il meccanismo di autenticazione più semplice supportato da HTTP e prevede che il client invii il nome utente e la password come testo codificato in base64 non crittografato. Il funzionamento è il seguente:

- Se un servizio Web riceve una richiesta per una risorsa protetta, rifiuta la richiesta con un codice di stato HTTP 401 (accesso negato) e imposta l'intestazione della risposta WWW-Authenticate, come illustrato nel diagramma seguente:

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Se un servizio Web riceve una richiesta per `Authorization` una risorsa protetta, con l'intestazione impostata correttamente, il servizio Web risponde con un codice di stato HTTP 200, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono nella risposta. Questo scenario è illustrato nel diagramma seguente:This scenario is shown in the following diagram:

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> L'autenticazione di base deve essere utilizzata solo su una connessione HTTPS. Se utilizzata tramite una `Authorization` connessione HTTP, l'intestazione può essere facilmente decodificata se il traffico HTTP viene acquisito da un utente malintenzionato.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Specifica dell'autenticazione di base in una richiesta WebSpecifying Basic Authentication in a Web Request

L'utilizzo dell'autenticazione di base è specificato come segue:

1. La stringa "Basic" viene `Authorization` aggiunta all'intestazione della richiesta.
1. Il nome utente e la password vengono combinati in una stringa con il formato "username:password", che viene quindi codificato in base64 e aggiunto all'intestazione `Authorization` della richiesta.

Pertanto, con un nome utente di 'XamarinUser' e una password di 'XamarinPassword', l'intestazione diventa:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La `HttpClient` classe può `Authorization` impostare `HttpClient.DefaultRequestHeaders.Authorization` il valore dell'intestazione nella proprietà. Poiché `HttpClient` l'istanza esiste `Authorization` tra più richieste, l'intestazione deve essere impostata una sola volta, anziché quando si effettua ogni richiesta, come illustrato nell'esempio di codice seguente:Because the instance exists across multiple requests, the header needs only to be set once, rather than when making every request, as shown in the following code example:

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

Quindi, quando viene effettuata una richiesta a un'operazione del servizio Web, la richiesta viene firmata con l'intestazione, `Authorization` che indica se l'utente dispone o meno dell'autorizzazione per richiamare l'operazione.

> [!IMPORTANT]
> Sebbene questo codice memorizzi le credenziali come costanti, non devono essere archiviate in un formato non sicuro in un'applicazione pubblicata.

## <a name="processing-the-authorization-header-server-side"></a>Elaborazione del lato server dell'intestazione di autorizzazioneProcessing the Authorization Header Server Side

Il servizio REST deve decorare `[BasicAuthentication]` ogni azione con l'attributo. Questo attributo viene `Authorization` utilizzato per analizzare l'intestazione e determinare se le credenziali con codifica base64 sono valide confrontandole con i valori archiviati in *Web.config*. Sebbene questo approccio sia adatto per un servizio di esempio, richiede l'estensione per un servizio Web pubblico.

Nel modulo di autenticazione di base utilizzato da IIS, gli utenti vengono autenticati in base alle credenziali di Windows. Pertanto, gli utenti devono disporre di account nel dominio del server. Tuttavia, il modello di autenticazione di base può essere configurato per consentire l'autenticazione personalizzata, in cui gli account utente vengono autenticati su un'origine esterna, ad esempio un database. Per altre informazioni, vedere [Autenticazione di base in ASP.NETAPI Web](https://www.asp.net/web-api/overview/security/basic-authentication) nel sito Web ASP.NET.

> [!NOTE]
> L'autenticazione di base non è stata progettata per gestire la disconnessione. Pertanto, l'approccio di autenticazione di base standard per la disconnessione consiste nel terminare la sessione.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di un servizio Web RESTfulConsume a RESTful web service](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
