---
title: L'autenticazione degli utenti con un servizio SimpleDB Amazon
description: "Amazon SimpleDB non offre un proprio sistema di autorizzazioni basate sulle risorse. Al contrario, l'autenticazione rispetto a un provider di identità consente di verificare che gli utenti dispongano dell'accesso ai propri dati solo nel dominio SimpleDB. In questo articolo viene illustrato come limitare l'accesso degli utenti ai propri dati SimpleDB."
ms.topic: article
ms.prod: xamarin
ms.assetid: 797C91A5-9720-4DAC-89D8-5C85996584C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 85413cf223e794ad2fda093601f9221d0261af39
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="authenticating-users-with-an-amazon-simpledb-service"></a>L'autenticazione degli utenti con un servizio SimpleDB Amazon

_Amazon SimpleDB non offre un proprio sistema di autorizzazioni basate sulle risorse. Al contrario, l'autenticazione rispetto a un provider di identità consente di verificare che gli utenti dispongano dell'accesso ai propri dati solo nel dominio SimpleDB. In questo articolo viene illustrato come limitare l'accesso degli utenti ai propri dati SimpleDB._

[Xamarin.Auth](https://github.com/xamarin/Xamarin.Auth) viene utilizzato nell'applicazione di esempio per il processo di autenticazione utente di gestire e archiviare in modo sicuro i dettagli dell'account degli utenti sul dispositivo. Per ulteriori informazioni vedere [autenticazione degli utenti con un Provider di identità](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="allowing-an-authenticated-user-access-to-simpledb-domain-data"></a>Consentendo un accesso utente autenticato al dominio SimpleDB dati

Applicazione di esempio utilizza la `TodoItem` classe ai dati del modello. Per archiviare un `TodoItem` istanza in un servizio SimpleDB deve prima essere convertito in un `List` di `ReplaceableAttribute` oggetti. Per ulteriori informazioni vedere [SimpleDB la creazione di oggetti](~/xamarin-forms/data-cloud/consuming/aws.md).

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

Per garantire che gli utenti hanno accesso solo ai propri dati nel dominio, SimpleDB il `ToSimpleDBReplaceableAttributes` metodo archivia un attributo aggiuntivo per un `TodoItem` dell'istanza, come illustrato nell'esempio di codice seguente:

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    ...
    new ReplaceableAttribute () {
      Name = "User",
      Value = App.User.Email,
      Replace = true
    },
  };
}
```

Questo attributo assicura che ogni elemento archiviato nel dominio SimpleDB abbia un indirizzo di posta elettronica associato per un utente, che viene utilizzato per identificare in modo univoco l'utente a cui per che appartengono i dati. Quando il contenuto di un dominio viene recuperato chiamando il `AmazonSimpleDBClient.SelectAsync` metodo, l'espressione di query garantisce che vengono recuperati solo gli elementi per l'utente autenticato, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
    SelectExpression = string.Format ("SELECT * from {0} WHERE User = '{1}'", tableName, App.User.Email)
  };
  var response = await client.SelectAsync (request);
  ...
}
```

Il `SelectAsync` metodo restituisce una risposta contenente una raccolta di elementi e attributi associati che corrispondono all'espressione di query. L'espressione di query garantisce che solo gli elementi che corrispondono a indirizzi di posta elettronica dell'utente verranno recuperati. Per ulteriori informazioni sulle espressioni di query, vedere [utilizzando selezionare questa opzione per creare query di Amazon SimpleDB](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) nel sito Web di Amazon.

> [!NOTE]
> **Nota**: prestare attenzione a seguire le regole di delimitazione quando si crea l'espressione di query. Per ulteriori informazioni, vedere [selezionare regole di citazione](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html) nel sito Web di Amazon.

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come limitare l'accesso degli utenti ai propri dati SimpleDB. Amazon SimpleDB non offre un proprio sistema di autorizzazioni basate sulle risorse. Al contrario, l'autenticazione rispetto a un provider di identità consente di verificare che gli utenti hanno accesso solo ai propri dati nel dominio SimpleDB.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAWSAuth (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWSAuth/)
- [Utilizzo di un servizio SimpleDB Amazon](~/xamarin-forms/data-cloud/consuming/aws.md)
- [L'autenticazione degli utenti con un Provider di identità](~/xamarin-forms/data-cloud/authentication/oauth.md)
- [Amazon Cognito Identity](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Documentazione per sviluppatori SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Amazon Web Services SDK per .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
