---
title: Utilizzo di un servizio SimpleDB Amazon
description: "Amazon SimpleDB è un servizio web che offre la possibilità di archiviare ed eseguire query su dati nel cloud di Amazon. In questo articolo viene illustrato come utilizzare AWS SDK per .NET per eseguire una query, creare, sostituire ed eliminare i dati archiviati in un servizio SimpleDB."
ms.topic: article
ms.prod: xamarin
ms.assetid: 823819AA-15F9-4144-B355-78A10AD37513
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 5604477ff182711a63ba2b0e6bbcbbcc082a76ed
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="consuming-an-amazon-simpledb-service"></a>Utilizzo di un servizio SimpleDB Amazon

_Amazon SimpleDB è un servizio web che offre la possibilità di archiviare ed eseguire query su dati nel cloud di Amazon. In questo articolo viene illustrato come utilizzare AWS SDK per .NET per eseguire una query, creare, sostituire ed eliminare i dati archiviati in un servizio SimpleDB._

Servizi SimpleDB usano un modello di richiesta e risposta familiare agli utenti di servizi REST. Le operazioni vengono richiamate per il servizio SimpleDB inviando una richiesta, che può contenere dati. Dopo l'elaborazione della richiesta, il servizio SimpleDB restituisce una risposta contenente i risultati. Un servizio SimpleDB deve essere creato a livello di codice e non può essere creato tramite il [Console di AWS](https://aws.amazon.com). Tuttavia, è necessario creare e accedere a qualsiasi servizio web di Amazon un account AWS.

In un servizio SimpleDB, i dati sono organizzati in domini, entro i quali possono essere inseriti i dati e si eseguono query sui dati. Domini sono costituiti da elementi che sono descritti da coppie nome-valore. I domini possono essere considerati siano simili alle tabelle, con gli attributi sono simili alle colonne e gli elementi siano simili alle righe. Per ulteriori informazioni sul modello di data SimpleDB, vedere [modello di dati](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/DataModel.html) nel sito Web di Amazon.

Istruzioni sulla configurazione di servizi richiesti Amazon sono reperibile nel file Leggimi che accompagna l'applicazione di esempio. Quando viene eseguita l'applicazione di esempio, si connetterà a un pool di identità Cognito di Amazon per autorizzare l'accesso al servizio SimpleDB, come illustrato nella schermata seguente:

![](aws-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="consuming-a-simpledb-service"></a>Utilizzo di un servizio SimpleDB

Identità Cognito Amazon consente ai servizi AWS, ad esempio SimpleDB, deve essere richiamato da un'applicazione senza credenziali AWS hardcoded nell'applicazione. In alternativa, in cui viene creato un pool di identità univoca nel [Amazon Cognito Console](https://console.aws.amazon.com/cognito/home). Il pool di identità contiene identità che usano i ruoli per specificare le risorse, ad esempio SimpleDB, che l'identità può accedere.

Il [AWS SDK per .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22) fornisce il `CognitoAWSCredentials` e `AmazonSimpleDBClient` classi, che vengono utilizzate da un'applicazione di xamarin. Forms per accedere al servizio SimpleDB, come illustrato nell'esempio di codice seguente:

```csharp
AmazonSimpleDBClient client;
...

public SimpleDBStorage ()
{
  var credentials = new CognitoAWSCredentials (
                      Constants.CognitoIdentityPoolId,
                      RegionEndpoint.USEast1);
  var config = new AmazonSimpleDBConfig ();
  config.RegionEndpoint = RegionEndpoint.USWest2;
  client = new AmazonSimpleDBClient (credentials, config);
  ...
}
```

Una nuova istanza di `CognitoAWSCredentials` classe viene creata, fornendo l'id univoco del pool e l'area dell'account identità Cognito. Al momento della scrittura, Cognito identità è disponibile solo nelle aree USEast1 ed EUWest1. Tuttavia, può comunicare con servizi di Amazon all'esterno di tali aree.

Quando il `AmazonSimpleDBClient` viene creata l'istanza, il `CognitoAWSCredentials` istanza deve essere fornita, insieme con un `AmazonSimpleDBConfig` istanza che specifica l'area geografica in cui risiede il servizio SimpleDB. Il `CognitoAWSCredentials` istanza assicura che il servizio SimpleDB a cui si accede sia quello associato all'account AWS in cui è stato creato il pool di identità, evitando la necessità di incorporare una chiave di accesso AWS e una chiave privata nell'applicazione.

Viene creato un dominio del servizio SimpleDB chiamando il `AmazonSimpleDBClient.CreateDomainAsync` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
string tableName = "Todo";
...

async Task CreateDomain ()
{
  ...
  await client.CreateDomainAsync (new CreateDomainRequest { DomainName = tableName });
  ...
}
```

Il `CreateDomainAsync` metodo richiede un `CreateDomainRequest` istanza come parametro. Il `CreateDomainRequest` istanza Inizializza il `DomainName` sul valore da utilizzare per identificare il dominio. Per creare il dominio, questo valore deve essere univoco tra i domini associati all'account AWS. In caso contrario, non verrà creato il dominio e per indicare che non verrà inviato alcuna risposta di errore. Qualsiasi operazione in base al nome di dominio verranno eseguito sul dominio esistente, piuttosto che un dominio appena creato.

### <a name="creating-simpledb-objects"></a>La creazione di oggetti SimpleDB

Applicazione di esempio utilizza la `TodoItem` classe ai dati del modello. Per archiviare un `TodoItem` istanza in un servizio SimpleDB deve prima essere convertito in un `List` di `ReplaceableAttribute` oggetti. Questa operazione viene eseguita la `ToSimpleDBReplaceableAttributes` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    new ReplaceableAttribute () {
      Name = "Name",
      Value = item.Name,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Notes",
      Value = item.Notes,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Done",
      Value = item.Done.ToString (),
      Replace = true
    }
  };
}
```

Questo metodo crea un `List` di nuovo `ReplaceableAttribute` istanze, con la `List` che rappresenta un singolo `TodoItem` istanza. Ogni `ReplaceableAttribute` istanza rappresenta una singola proprietà di `TodoItem` istanza. Per ulteriori informazioni sul `ReplaceableAttribute` classe, vedere [ReplaceableAttribute classe](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_ReplaceableAttribute.htm) nel sito Web di Amazon.

Analogamente, quando i dati vengono recuperati dal servizio SimpleDB, deve essere convertita da un `List` di `Attribute` istanze per un `TodoItem` istanza. Questa operazione viene eseguita con il `FromSimpleDBAttributes` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
TodoItem FromSimpleDBAttributes (List<Amazon.SimpleDB.Model.Attribute> attributeList, string id)
{
  var todoItem = new TodoItem ();
  todoItem.ID = id;
  todoItem.Name = attributeList.Where (attr => attr.Name == "Name").FirstOrDefault ().Value;
  todoItem.Notes = attributeList.Where (attr => attr.Name == "Notes").FirstOrDefault ().Value;
  todoItem.Done = Convert.ToBoolean (attributeList.Where (attr => attr.Name == "Done").FirstOrDefault ().Value);
  return todoItem;
}
```

Questo metodo recupera semplicemente ogni `Attribute` istanza il `List` e lo imposta appena creato `TodoItem` istanza.

Per ulteriori informazioni sul `Attribute` classe, vedere [classe Attribute](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_Attribute.htm) nel sito Web di Amazon.

### <a name="querying-data"></a>Esecuzione di query su dati

Il contenuto di un dominio può essere recuperato chiamando il `AmazonSimpleDBClient.SelectAsync` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
      SelectExpression = string.Format ("SELECT * from {0}", tableName)
  };
  var response = await client.SelectAsync (request);
  foreach (var item in response.Items) {
    Items.Add (FromSimpleDBAttributes (item.Attributes, item.Name));
  }
  ...
}
```

Il `SelectAsync` metodo accetta un `SelectRequest` istanza come parametro, che specifica un `Select` di espressione di query `SelectExpression` proprietà. Il formato dell'espressione di query è simile al formato dello standard SQL `SELECT` istruzione. Per ulteriori informazioni sull'espressione di query, vedere [utilizzando selezionare questa opzione per creare query di Amazon SimpleDB](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) nel sito Web di Amazon.

> [!NOTE]
> Assicurarsi di seguire le regole di delimitazione quando si crea l'espressione di query. Per ulteriori informazioni, vedere [selezionare regole di citazione](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html) nel sito Web di Amazon.

Il `SelectAsync` metodo restituisce una risposta contenente una raccolta di elementi e attributi associati che corrispondono all'espressione di query. Questa raccolta viene quindi convertita in un `List` di `TodoItem` istanze per la visualizzazione.

### <a name="creating-and-replacing-data"></a>Creazione e la sostituzione di dati

Il `AmazonSimpleDBClient.PutAttributesAsync` metodo viene utilizzato per creare e sostituire i dati nel dominio del servizio SimpleDB, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBReplaceableAttributes (todoItem);
  var request = new PutAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.PutAttributesAsync (request);
  ...
}
```

Il `PutAttributesAsync` metodo accetta un `PutAttributesRequest` istanza come parametro. Il `PutAttributesRequest` istanza specifica le coppie nome-valore di attributo che può essere creato come nuovo elemento o sostituiti in un elemento esistente. Il `List` di `ReplaceableAttribute` compilata istanze il `ToSimpleDBReplaceableAttributes` metodo. Questo metodo imposta inoltre il `Replace` proprietà di ogni `ReplaceableAttribute` a `true`. In questo modo il nuovo valore dell'attributo sostituire un valore di attributo esistenti se i dati viene sostituiti. Tuttavia, il tentativo di sostituire i valori di attributo che non esistono non comporterà una risposta di errore.

Il valore di `PutAttributesRequest.ItemName` proprietà controlla se un nuovo elemento verrà aggiunto al dominio o se un elemento esistente verrà sostituito. Quando l'applicazione crea un nuovo elemento, viene impostato il `TodoItem.ID` proprietà a un nuovo `Guid`. Ciò garantisce che ogni `TodoItem` istanza dispone di un identificatore univoco. Pertanto, se il `PutAttributesRequest.ItemName` è impostata su un valore che non esiste nel dominio, il servizio SimpleDB verrà creato un nuovo elemento che contiene le coppie nome-valore di attributo specificato. Se il `PutAttributesRequest.ItemName` è impostata su un valore che esiste già nel dominio, il servizio SimpleDB aggiornerà l'elemento con le coppie nome-valore di attributo specificato.

### <a name="deleting-data"></a>Eliminazione di dati

Il `AmazonSimpleDBClient.DeleteAttributesAsync` metodo viene utilizzato per eliminare i dati dal dominio del servizio SimpleDB, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBAttributes (todoItem);
  var request = new DeleteAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.DeleteAttributesAsync (request);
  ...
}
```

Il `DeleteAttributesAsync` metodo accetta un `DeleteAttributesRequest` istanza come parametro.  Il `DeleteAttributesRequest` istanza specifica gli attributi che devono essere eliminate dall'elemento, con il `List` di `Attribute` istanze da eliminare dalla fase di compilazione di `ToSimpleDBAttributes` metodo. L'elemento viene eliminato, purché tutti gli attributi dell'elemento vengono eliminati.

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come usare AWS SDK per .NET per eseguire una query, creare e sostituire ed eliminare i dati archiviati in un servizio SimpleDB. Questo SDK offre la `CognitoAWSCredentials` e `AmazonSimpleDBClient` le classi utilizzate per accedere al servizio SimpleDB da un'applicazione di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAWS (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS/)
- [Guida per sviluppatori Xamarin SDK dei servizi Web di Amazon](http://docs.aws.amazon.com/mobile/sdkforxamarin/developerguide/)
- [Amazon Cognito Identity](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Documentazione per sviluppatori SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Classe AmazonSimpleDBClient](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_AmazonSimpleDBClient.htm)
- [Amazon Web Services SDK per .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
