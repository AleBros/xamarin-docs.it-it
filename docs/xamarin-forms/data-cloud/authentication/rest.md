---
title: L'autenticazione di un servizio Web RESTful
description: HTTP supporta l'utilizzo di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse per solo i client che hanno le credenziali corrette. In questo articolo viene illustrato come utilizzare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 3160fcced190f74fcfb44c6259451e00995de0fa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-a-restful-web-service"></a>L'autenticazione di un servizio Web RESTful

_HTTP supporta l'utilizzo di diversi meccanismi di autenticazione per controllare l'accesso alle risorse. L'autenticazione di base fornisce l'accesso alle risorse per solo i client che hanno le credenziali corrette. In questo articolo viene illustrato come utilizzare l'autenticazione di base per proteggere l'accesso alle risorse del servizio web RESTful._

L'applicazione di esempio xamarin. Forms accompagnamento utilizza un servizio ospitato Xamarin REST che fornisce l'accesso in sola lettura per il servizio web. Le operazioni di creano, aggiornano ed eliminare dati, pertanto, non eliminerà i dati utilizzati nell'applicazione. È tuttavia disponibile in una versione eseguibile del servizio REST di *TodoRESTService* cartella nell'applicazione di esempio e istruzioni su come configurare il servizio può essere trovata non esiste. Questa versione eseguibile del servizio REST fornisce full creazione, eliminazione, lettura e aggiornamento di accedere ai dati.

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>L'autenticazione degli utenti tramite HTTP

L'autenticazione di base è il meccanismo di autenticazione più semplice supportato da HTTP e include il client invia il nome utente e la password come testo con codificata base64 non crittografata. Funziona come indicato di seguito:

- Se un servizio web riceve una richiesta per una risorsa protetta, rifiuta la richiesta con un codice di stato HTTP 401 (accesso negato) e lo imposta l'intestazione WWW-Authenticate della risposta, come illustrato nel diagramma seguente:

![](rest-images/basic-authentication-fail.png "Errore di autenticazione di base")

- Se un servizio web riceve una richiesta per una risorsa protetta, con la `Authorization` intestazione impostato correttamente, il web servizio risponde con un codice di stato HTTP 200, indicante che la richiesta ha avuto esito positivo e che le informazioni richieste nella risposta. Questo scenario è illustrato nel diagramma seguente:

![](rest-images/basic-authentication-success.png "L'autenticazione di base successiva")

> [!NOTE]
> L'autenticazione di base deve essere utilizzato solo con una connessione HTTPS. Se utilizzato su una connessione HTTP, il <code>Authorization</code> intestazione può essere decodificata facilmente se il traffico HTTP viene acquisito da un utente malintenzionato.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Specifica l'autenticazione di base in una richiesta Web

Utilizzo dell'autenticazione di base viene specificata come segue:

1. La stringa "Base" viene aggiunto per il `Authorization` intestazione della richiesta.
1. Il nome utente e password vengono combinati in una stringa con il formato "nomeutente: password", che è quindi codifica base64 e aggiungere il `Authorization` intestazione della richiesta.

Pertanto, con un nome utente di 'XamarinUser' e la password 'XamarinPassword', l'intestazione diventa:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

Il `HttpClient` classe può impostare il `Authorization` sul valore dell'intestazione di `HttpClient.DefaultRequestHeaders.Authorization` proprietà. Poiché il `HttpClient` istanza esiste in più richieste, il `Authorization` intestazione solo deve essere impostata una sola volta, anziché quando ogni richiesta, come illustrato nell'esempio di codice seguente:

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

Quindi quando viene effettuata una richiesta per un'operazione del servizio web la richiesta è firmata con la `Authorization` intestazione, che indica se l'utente dispone dell'autorizzazione per richiamare l'operazione.

> [!NOTE]
> Mentre il servizio REST di esempio vengono archiviate le credenziali come costanti, non devono essere archiviati in un formato non protetto in un'applicazione pubblicata. Il [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet fornisce funzionalità di archiviazione in modo sicuro le credenziali. Per ulteriori informazioni vedere [l'archiviazione e recupero di informazioni sull'account nei dispositivi](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="processing-the-authorization-header-server-side"></a>Elaborazione sul lato Server intestazione di autorizzazione

Il servizio REST di esempio accompagnamento decora ogni azione con la `[BasicAuthentication]` attributo. Questo attributo viene implementato dal `BasicAuthenticationAttribute` nella soluzione e viene utilizzata per analizzare il `Authorization` intestazione e determinare se le credenziali con codificata base 64 vengono confrontandole con i valori archiviati *Web. config*. Mentre questo approccio è adatto per il servizio di esempio, è necessario estendere un servizio web pubblico.

Nel modulo di autenticazione di base usato da IIS, gli utenti vengono autenticati con le proprie credenziali di Windows. Di conseguenza, gli utenti devono disporre di account nel dominio del server. Tuttavia, il modello di autenticazione di base può essere configurato per consentire l'autenticazione personalizzata, in cui gli account utente autenticati un'origine esterna, ad esempio un database. Per ulteriori informazioni vedere [l'autenticazione di base in ASP.NET Web API](http://www.asp.net/web-api/overview/security/basic-authentication) nel sito Web ASP.NET.

> [!NOTE]
> L'autenticazione di base non è stato progettato per gestire la disconnessione. Pertanto, l'approccio di autenticazione di base standard per la disconnessione è per terminare la sessione.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come aggiungere l'autenticazione di base per le richieste web effettuate da un'applicazione di xamarin. Forms con il `HttpClient` classe. L'autenticazione di base fornisce l'accesso alle risorse per solo i client che hanno le credenziali corrette. Per informazioni su come usare [Xamarin.Auth](https://www.nuget.org/packages/Xamarin.Auth/) per gestire il processo di autenticazione in un'applicazione di xamarin. Forms in modo che gli utenti possono condividere un back-end durante l'accesso solo ai dati, vedere [autenticazione degli utenti con un Provider di identità](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="related-links"></a>Collegamenti correlati

- [TodoREST (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [Utilizzo di un servizio web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
