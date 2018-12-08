---
title: L'autenticazione di un servizio Web RESTful
description: L'autenticazione di base fornisce l'accesso alle risorse solo ai client che hanno le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: e2ab6c053901ad6c1668c5ae5be9ab04d9d05e8a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050337"
---
# <a name="authenticating-a-restful-web-service"></a>L'autenticazione di un servizio Web RESTful

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)

_HTTP supporta l'uso di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse solo ai client che hanno le credenziali corrette. Questo articolo illustra come usare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful._

L'applicazione di esempio xamarin. Forms accompagnamento utilizza un servizio REST basato su Xamarin che fornisce accesso in lettura al servizio web. Di conseguenza, le operazioni che creano, aggiornano ed eliminare dati non modificheranno i dati usati nell'applicazione. Tuttavia, è disponibile in una versione eseguibile del servizio REST di *TodoRESTService* cartella in cui l'applicazione di esempio e istruzioni su come configurare il servizio è possibile trovare. Questa versione del servizio REST hostable fornisce full create, read, update e delete di accesso ai dati.

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
  HttpClient client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    client = new HttpClient ();
    ...
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

Quindi quando viene effettuata una richiesta per un'operazione del servizio web la richiesta viene firmata con la `Authorization` intestazione, che indica se l'utente dispone dell'autorizzazione per richiamare l'operazione.

> [!NOTE]
> Mentre il servizio REST di esempio archivia le credenziali come costanti, non devono essere archiviati in un formato non protetto in un'applicazione pubblicata. Il [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet fornisce funzionalità per l'archiviazione sicura delle credenziali. Per altre informazioni, vedere [memorizzazione e recupero di informazioni sull'account nei dispositivi](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="processing-the-authorization-header-server-side"></a>Elaborazione sul lato Server dell'intestazione di autorizzazione

Il servizio REST di esempio associato decora ogni azione con il `[BasicAuthentication]` attributo. Questo attributo viene implementato dal `BasicAuthenticationAttribute` classe nella soluzione e viene usato per analizzare le `Authorization` intestazione e determinare se le credenziali con codificata base 64 vengono confrontandole con i valori archiviati in *Web. config*. Benché questo approccio è adatto per il servizio di esempio, richiede l'estensione per un servizio web rivolte al pubblico.

Nel modulo di autenticazione di base usato da IIS, gli utenti vengono autenticati con le proprie credenziali di Windows. Di conseguenza, gli utenti devono avere account nel dominio del server. Tuttavia, il modello di autenticazione di base può essere configurato per consentire l'autenticazione personalizzata, in cui gli account utente sono autenticati da un'origine esterna, ad esempio un database. Per altre informazioni, vedere [autenticazione di base nell'API Web ASP.NET](http://www.asp.net/web-api/overview/security/basic-authentication) sul sito Web ASP.NET.

> [!NOTE]
> L'autenticazione di base non è stato progettato per gestire la disconnessione. Pertanto, l'approccio di autenticazione di base standard per la disconnessione è per terminare la sessione.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come aggiungere l'autenticazione di base per le richieste web effettuate da un'applicazione xamarin. Forms con i `HttpClient` classe. L'autenticazione di base fornisce l'accesso alle risorse solo ai client che hanno le credenziali corrette. Per informazioni su come usare [AUTH](https://www.nuget.org/packages/Xamarin.Auth/) per gestire il processo di autenticazione in un'applicazione xamarin. Forms in modo che gli utenti possono condividere un back-end mantenendo solo l'accesso ai dati, vedere [autenticazione degli utenti con un Provider di identità](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="related-links"></a>Collegamenti correlati

- [TodoREST (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [Utilizzo di un servizio web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
