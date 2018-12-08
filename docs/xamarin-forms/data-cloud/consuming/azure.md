---
title: Utilizzo di un'App per dispositivi mobili di Azure
description: Questo articolo, che è applicabile solo alle App per dispositivi mobili di Azure che usano un back-end Node. js, illustra come eseguire una query, inserire, aggiornare ed eliminare i dati archiviati in una tabella in un'istanza di App per dispositivi mobili di Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1ac68992d36627eb5d6aee0d4d19564ce63a3936
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052135"
---
# <a name="consuming-an-azure-mobile-app"></a>Utilizzo di un'App per dispositivi mobili di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Le App per dispositivi mobili di Azure consentono di sviluppare App con back-end scalabile ospitati nel servizio App di Azure con supporto per l'autenticazione per dispositivi mobili, la sincronizzazione offline e notifiche push. Questo articolo, che è applicabile solo alle App per dispositivi mobili di Azure che usano un back-end Node. js, illustra come eseguire una query, inserire, aggiornare ed eliminare i dati archiviati in una tabella in un'istanza di App per dispositivi mobili di Azure._

> [!NOTE]
> A partire dal 30 giugno, tutte le nuove app per dispositivi mobili di Azure verrà creata con TLS 1.2 per impostazione predefinita. Inoltre, è anche consigliabile esistenti per usare TLS 1.2 riconfigurare le App per dispositivi mobili di Azure. Per informazioni su come applicare TLS 1.2 in un'App per dispositivi mobili di Azure, vedere [imporre TLS 1.2](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-1112). Per informazioni su come configurare i progetti Xamarin per usare TLS 1.2, vedere [Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Per informazioni su come creare un'istanza di App per dispositivi mobili di Azure che può essere usata da xamarin. Forms, vedere [creare un'app xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Dopo aver seguito le istruzioni che seguono, l'applicazione di esempio scaricabili può essere configurata per utilizzare l'istanza di App per dispositivi mobili di Azure tramite l'impostazione di `Constants.ApplicationURL` all'URL dell'istanza di App per dispositivi mobili di Azure. Quindi, quando viene eseguita l'applicazione di esempio si connetterà all'istanza di App per dispositivi mobili di Azure, come illustrato nello screenshot seguente:

![](azure-images/portal.png "Applicazione di esempio")

Accesso all'App per dispositivi mobili di Azure avviene tramite il [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), e tutte le connessioni dall'applicazione di esempio xamarin. Forms in Azure vengono eseguite su HTTPS.

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Utilizzo di un'istanza di App per dispositivi mobili di Azure

Il [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornisce il `MobileServiceClient` classe, che viene usato da un'applicazione xamarin. Forms per accedere all'istanza di App per dispositivi mobili di Azure, come illustrato nell'esempio di codice seguente:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Quando il `MobileServiceClient` istanza viene creata, deve essere specificato un URL di applicazione per identificare l'istanza di App per dispositivi mobili di Azure. Questo valore può essere ottenuto dal dashboard per l'app per dispositivi mobili nel [portale di Microsoft Azure](https://portal.azure.com/).

Un riferimento al `TodoItem` tabella archiviata nell'istanza di App per dispositivi mobili di Azure deve essere ottenuta prima di possono eseguire operazioni su tale tabella. Questo risultato viene ottenuto chiamando il `GetTable` metodo sul `MobileServiceClient` dell'istanza, che restituisce un `IMobileServiceTable<TodoItem>` riferimento.

### <a name="querying-data"></a>Esecuzione di query su dati

Il contenuto di una tabella può essere recuperato chiamando il `IMobileServiceTable.ToEnumerableAsync` metodo che valuta la query e restituisce i risultati in modo asincrono. Dati inoltre possono essere filtrati sul lato server includendo un `Where` clausola della query. Il `Where` clausola applica un predicato per la query sulla tabella, del filtro di riga, come illustrato nell'esempio di codice seguente:

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

Questa query restituisce tutti gli elementi di `TodoItem` tabella i cui `Done` è uguale alla proprietà `false`. I risultati della query vengono quindi inseriti un `ObservableCollection` per la visualizzazione.

### <a name="inserting-data"></a>Inserimento di dati

Quando si inseriscono dati nell'istanza di App per dispositivi mobili di Azure, verranno generate automaticamente nuove colonne nella tabella in base alle esigenze, fornito lo schema dinamico è abilitato nell'istanza di App per dispositivi mobili di Azure. Il `IMobileServiceTable.InsertAsync` metodo viene utilizzato per inserire una nuova riga dei dati nella tabella specificata, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Quando si effettua una richiesta di inserimento, un ID non deve essere specificato nei dati passati all'istanza di App per dispositivi mobili di Azure. Se la richiesta di inserimento contiene un ID di un `MobileServiceInvalidOperationException` verrà generata.

Dopo il `InsertAsync` metodo viene completato, l'ID dei dati nell'istanza di App per dispositivi mobili di Azure verrà compilato nel `TodoItem` istanza dell'applicazione xamarin. Forms.

### <a name="updating-data"></a>Aggiornamento di dati

Quando si aggiornano i dati nell'istanza di App per dispositivi mobili di Azure, verranno generate automaticamente nuove colonne nella tabella in base alle esigenze, fornito lo schema dinamico è abilitato nell'istanza di App per dispositivi mobili di Azure. Il `IMobileServiceTable.UpdateAsync` metodo viene utilizzato per aggiornare i dati esistenti con nuove informazioni, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Quando si effettua una richiesta di aggiornamento, un ID deve essere specificato in modo che l'istanza di App per dispositivi mobili di Azure può identificare i dati da aggiornare. Questo ID viene archiviato il `TodoItem.ID` proprietà. Se la richiesta di aggiornamento non contiene un ID non è possibile per l'istanza di App per dispositivi mobili di Azure determinare i dati da aggiornare e quindi un `MobileServiceInvalidOperationException` verrà generata.

### <a name="deleting-data"></a>Eliminazione di dati

Il `IMobileServiceTable.DeleteAsync` metodo viene utilizzato per eliminare i dati da una tabella di App per dispositivi mobili di Azure, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Quando si effettua una richiesta di eliminazione, un ID deve essere specificato in modo che l'App per dispositivi mobili di Azure sinstance possibile identificare i dati da eliminare. Questo ID viene archiviato il `TodoItem.ID` proprietà. Se la richiesta di eliminazione non contiene un ID, non è possibile per l'istanza di App per dispositivi mobili di Azure determinare i dati da eliminare, quindi un `MobileServiceInvalidOperationException` verrà generata.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare il [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) per eseguire una query, inserire, aggiornare ed eliminare i dati archiviati in una tabella in un'istanza di App per dispositivi mobili di Azure. il SDK fornisce il `MobileServiceClient` classe utilizzata da un'applicazione xamarin. Forms per accedere all'istanza di App per dispositivi mobili di Azure.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Creare un'app xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
