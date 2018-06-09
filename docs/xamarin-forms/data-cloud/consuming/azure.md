---
title: Utilizzo di un'App per dispositivi mobili di Azure
description: Questo articolo è applicabile solo alle App mobili di Azure che utilizzano un back-end di Node.js, viene illustrato come eseguire una query, inserimento, aggiornamento ed eliminare i dati archiviati in una tabella in un'istanza di App mobili di Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 3cf27e48fe23b9a0f035689e55a72fcc706ab266
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241331"
---
# <a name="consuming-an-azure-mobile-app"></a>Utilizzo di un'App per dispositivi mobili di Azure

_Azure App per dispositivi mobili consentono di sviluppare applicazioni con scalabilità back-end ospitato in Azure App Service, con supporto per l'autenticazione per dispositivi mobili, non in linea sincronizzazione e le notifiche push. Questo articolo è applicabile solo alle App mobili di Azure che utilizzano un back-end di Node.js, viene illustrato come eseguire una query, inserimento, aggiornamento ed eliminare i dati archiviati in una tabella in un'istanza di App mobili di Azure._

Per informazioni su come creare un'istanza di App mobili di Azure che può essere utilizzata da xamarin. Forms, vedere [creare un'app xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Dopo aver seguito le istruzioni, l'applicazione di esempio scaricabile può essere configurata per utilizzare l'istanza di App mobili di Azure mediante l'impostazione di `Constants.ApplicationURL` all'URL dell'istanza di App mobili di Azure. Quindi, quando viene eseguita l'applicazione di esempio si connetterà all'istanza di App mobili di Azure, come illustrato nella schermata seguente:

![](azure-images/portal.png "Applicazione di esempio")

Accesso alle applicazioni per dispositivi mobili di Azure viene eseguita tramite il [Client mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), e tutte le connessioni dall'applicazione di esempio xamarin. Forms in Azure vengono eseguite tramite HTTPS.

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Utilizzo di un'istanza di Azure App per dispositivi mobili

Il [Client mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornisce il `MobileServiceClient` (classe), che viene usato da un'applicazione di xamarin. Forms per accedere all'istanza di App mobili di Azure, come mostrato nell'esempio di codice seguente:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Quando il `MobileServiceClient` istanza viene creata, specificare un URL dell'applicazione per identificare l'istanza di App mobili di Azure. Questo valore può essere ottenuto dal dashboard per l'app per dispositivi mobili nel [portale di Microsoft Azure](https://portal.azure.com/).

Un riferimento di `TodoItem` tabella archiviata nell'istanza di App mobili di Azure deve essere ottenuta prima di possono eseguire operazioni su tale tabella. Questo risultato viene ottenuto chiamando il `GetTable` metodo il `MobileServiceClient` istanza, che restituisce un `IMobileServiceTable<TodoItem>` riferimento.

### <a name="querying-data"></a>Esecuzione di query su dati

Il contenuto di una tabella può essere recuperato chiamando il `IMobileServiceTable.ToEnumerableAsync` metodo che valuta la query in modo asincrono e restituisce i risultati. Dati inoltre possono essere filtrati lato server includendo un `Where` clausola della query. Il `Where` clausola applica un predicato per la query sulla tabella, del filtro di riga, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

Questa query restituisce tutti gli elementi di `TodoItem` tabella i cui `Done` proprietà è uguale a `false`. I risultati della query vengono quindi inseriti un `ObservableCollection` per la visualizzazione.

### <a name="inserting-data"></a>Inserimento di dati

Quando si inseriscono dati nell'istanza di App mobili di Azure, verranno generate automaticamente nuove colonne nella tabella in base alle esigenze, fornito a tale schema dinamico è abilitato nell'istanza di App mobili di Azure. Il `IMobileServiceTable.InsertAsync` metodo viene utilizzato per inserire una nuova riga di dati della tabella specificata, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Quando si effettua una richiesta di inserimento, un ID non deve essere specificato in dati passati all'istanza di App mobili di Azure. Se la richiesta di inserimento contiene un ID di un `MobileServiceInvalidOperationException` verrà generata.

Dopo il `InsertAsync` metodo viene completato, l'ID dei dati nell'istanza di App mobili di Azure verrà popolato nel `TodoItem` istanza dell'applicazione di xamarin. Forms.

### <a name="updating-data"></a>Aggiornamento di dati

Quando si aggiornano i dati nell'istanza di App mobili di Azure, verranno generate automaticamente nuove colonne nella tabella in base alle esigenze, fornito a tale schema dinamico è abilitato nell'istanza di App mobili di Azure. Il `IMobileServiceTable.UpdateAsync` metodo viene utilizzato per aggiornare i dati esistenti con nuove informazioni, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Quando si effettua una richiesta di aggiornamento, un ID deve essere specificato in modo che l'istanza di App mobili di Azure è possibile identificare i dati da aggiornare. Questo valore viene archiviato nel `TodoItem.ID` proprietà. Se la richiesta di aggiornamento non contiene un ID non è possibile per l'istanza di App mobili di Azure determinare i dati da aggiornare e quindi un `MobileServiceInvalidOperationException` verrà generata.

### <a name="deleting-data"></a>Eliminazione di dati

Il `IMobileServiceTable.DeleteAsync` metodo viene utilizzato per eliminare i dati da una tabella di App mobili di Azure, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Quando si effettua una richiesta di eliminazione, deve essere specificato un ID in modo che il sinstance App per dispositivi mobili di Azure è possibile identificare i dati da eliminare. Questo valore viene archiviato nel `TodoItem.ID` proprietà. Se la richiesta di eliminazione non contiene un ID, non è possibile per l'istanza di App mobili di Azure determinare i dati da eliminare, quindi un `MobileServiceInvalidOperationException` verrà generata.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare il [Client mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) per eseguire una query, inserimento, aggiornamento ed eliminare i dati archiviati in una tabella in un'istanza di App mobili di Azure. il SDK fornisce il `MobileServiceClient` classe utilizzata da un'applicazione di xamarin. Forms per accedere all'istanza di App mobili di Azure.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Creare un'app xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Client per dispositivi mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
