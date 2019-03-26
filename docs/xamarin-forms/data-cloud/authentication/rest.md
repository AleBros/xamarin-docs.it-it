---
title: L'autenticazione di un servizio Web RESTful
description: L'autenticazione di base fornisce l'accesso alle risorse solo ai client che hanno le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: d3f07a72ee26d6be4fafa72137dc9b6c3a724e00
ms.sourcegitcommit: 086edd9c44dfc0e77412e1ed5eda7318bbd1ce7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58477343"
---
# <a name="authenticating-a-restful-web-service"></a>L'autenticazione di un servizio Web RESTful

_HTTP supporta l'uso di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse solo ai client che hanno le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful._

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>L'autenticazione degli utenti tramite HTTP

L'autenticazione di base è il meccanismo di autenticazione più semplice supportato da HTTP e include il client che invia il nome utente e la password come testo non crittografato con codificata base 64. Funziona come indicato di seguito:

- Se un servizio web riceve una richiesta per una risorsa protetta, rifiuta la richiesta con codice di stato HTTP 401 (accesso negato) e imposta l'intestazione di risposta WWW-Authenticate, come illustrato nel diagramma seguente:

![](rest-images/basic-authentication-fail.png "Errore di autenticazione di base")

- Se un servizio web riceve una richiesta per una risorsa protetta, con la `Authorization` intestazione impostato correttamente, il web servizio risponde con un codice di stato HTTP 200, indicante che la richiesta ha avuto esito positivo e che le informazioni richieste nella risposta. Questo scenario è illustrato nel diagramma seguente:

![](rest-images/basic-authentication-success.png "L'autenticazione di base successiva")

> [!NOTE]
> L'autenticazione di base deve essere utilizzato solo con una connessione HTTPS. Se utilizzato su una connessione HTTP, il <code>Authorization</code> intestazione può essere decodificata con facilità se il traffico HTTP viene acquisito da un utente malintenzionato.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Specifica l'autenticazione di base in una richiesta Web

Uso dell'autenticazione di base viene specificata come segue:

1. La stringa "Basic" viene aggiunto al `Authorization` intestazione della richiesta.
1. Il nome utente e password vengono combinati in una stringa con il formato "nomeutente: password", che è quindi codifica base64 e aggiunto al `Authorization` intestazione della richiesta.

Pertanto, con un nome utente di 'XamarinUser' e la password 'XamarinPassword', l'intestazione diventa:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

Il `HttpClient` classe può impostare il `Authorization` sul valore dell'intestazione di `HttpClient.DefaultRequestHeaders.Authorization` proprietà. Poiché il `HttpClient` istanza esiste tra più richieste, il `Authorization` intestazione è solo necessario impostare una sola volta, anziché quando ogni richiesta, come illustrato nell'esempio di codice seguente:

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

Quindi quando viene effettuata una richiesta per un'operazione del servizio web la richiesta viene firmata con la `Authorization` intestazione, che indica se l'utente dispone dell'autorizzazione per richiamare l'operazione.

> [!NOTE]
> Mentre questo codice vengono archiviate le credenziali come costanti, non devono essere archiviati in un formato non protetto in un'applicazione pubblicata. Il [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet fornisce funzionalità per l'archiviazione sicura delle credenziali. Per altre informazioni, vedere [memorizzazione e recupero di informazioni sull'account nei dispositivi](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="processing-the-authorization-header-server-side"></a>Elaborazione sul lato Server dell'intestazione di autorizzazione

Il servizio REST deve decorare con ogni azione di `[BasicAuthentication]` attributo. Questo attributo viene usato per analizzare le `Authorization` intestazione e determinare se le credenziali con codificata base 64 vengono confrontandole con i valori archiviati nel *Web. config*. Sebbene questo approccio è adatto per un esempio di servizio, è necessario per l'estensione per un servizio web rivolte al pubblico.

Nel modulo di autenticazione di base usato da IIS, gli utenti vengono autenticati con le proprie credenziali di Windows. Di conseguenza, gli utenti devono avere account nel dominio del server. Tuttavia, il modello di autenticazione di base può essere configurato per consentire l'autenticazione personalizzata, in cui gli account utente sono autenticati da un'origine esterna, ad esempio un database. Per altre informazioni, vedere [autenticazione di base nell'API Web ASP.NET](http://www.asp.net/web-api/overview/security/basic-authentication) sul sito Web ASP.NET.

> [!NOTE]
> L'autenticazione di base non è stato progettato per gestire la disconnessione. Pertanto, l'approccio di autenticazione di base standard per la disconnessione è per terminare la sessione.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di un servizio web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
