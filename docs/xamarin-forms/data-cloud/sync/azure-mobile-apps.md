---
title: La sincronizzazione Offline dei dati con App per dispositivi mobili di Azure
description: Questo articolo illustra come aggiungere funzionalità di sincronizzazione offline per un'applicazione xamarin. Forms che utilizza un back-end dell'App per dispositivi mobili di Azure.
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 6080b4dc152558d6f532399cee7424670c588c28
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319575"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>La sincronizzazione Offline dei dati con App per dispositivi mobili di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)

_La sincronizzazione offline consente agli utenti di interagire con un'applicazione per dispositivi mobili, visualizzazione, aggiunta o modifica dei dati, anche se non c'è una connessione di rete. Le modifiche vengono archiviate in un database locale e quando il dispositivo è online, le modifiche possono essere sincronizzate con l'istanza di App per dispositivi mobili di Azure. Questo articolo illustra come aggiungere funzionalità di sincronizzazione offline per un'applicazione xamarin. Forms._

## <a name="overview"></a>Panoramica

Il [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornisce il `IMobileServiceTable` interfaccia che rappresenta le operazioni che possono essere eseguite nelle tabelle archiviate nell'istanza di App per dispositivi mobili di Azure. Queste operazioni connettersi direttamente all'istanza di App per dispositivi mobili di Azure e avrà esito negativo se il dispositivo mobile non dispone di una connessione di rete.

Per supportare la sincronizzazione offline, Azure Mobile Client SDK supporta *sincronizzare le tabelle*, che vengono fornite dal `IMobileServiceSyncTable` interfaccia. Questa interfaccia fornisce la stessa creazione, lettura, aggiornamento, le operazioni di eliminazione (CRUD) come il `IMobileServiceTable` interfaccia, ma le operazioni di lettura o scrittura in un archivio locale. L'archivio locale non è popolato con i nuovi dati dall'istanza di App per dispositivi mobili di Azure fino a quando non viene eseguita una chiamata a *pull* dei dati. Analogamente, i dati non viene inviati all'istanza di App per dispositivi mobili di Azure fino a quando non viene eseguita una chiamata a *push* le modifiche locali.

Sincronizzazione offline include anche il supporto per il rilevamento dei conflitti quando lo stesso record viene modificato in entrambi nell'archivio locale e nell'istanza di App per dispositivi mobili di Azure e la risoluzione dei conflitti personalizzato. I conflitti possono essere gestiti sia nell'archivio locale o nell'istanza di App per dispositivi mobili di Azure.

Per altre informazioni sulla sincronizzazione offline, vedere [sincronizzazione dati Offline nelle App per dispositivi mobili di Azure](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) e [abilitare la sincronizzazione offline per l'app per dispositivi mobili xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di sincronizzazione offline tra un'applicazione xamarin. Forms e un'istanza di App per dispositivi mobili di Azure è come segue:

1. Creare un'istanza di App per dispositivi mobili di Azure. Per altre informazioni, vedere [utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Aggiungere il [Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) pacchetto NuGet per tutti i progetti nella soluzione xamarin. Forms.
1. (Facoltativo) Abilitare l'autenticazione nell'istanza di App per dispositivi mobili di Azure e l'applicazione xamarin. Forms. Per altre informazioni, vedere [autenticare gli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

La sezione seguente fornisce le istruzioni di configurazione aggiuntive per la configurazione dei progetti di Universal Windows Platform (UWP) usare il pacchetto Microsoft.Azure.Mobile.Client.SQLiteStore NuGet. Nessuna configurazione aggiuntiva è necessario usare il pacchetto Microsoft.Azure.Mobile.Client.SQLiteStore NuGet in iOS e Android.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Per utilizzare SQLite in Universal Windows Platform (UWP), seguire questa procedura:

1. Installare il [SQLite per Universal Windows Platform](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) estensione di Visual Studio nell'ambiente di sviluppo.
1. Nel progetto UWP in Visual Studio, fare clic con il pulsante destro **riferimenti > Aggiungi riferimento**, passare alla **estensioni** e aggiungere il **SQLite for Universal Windows Platform** e **Visual C++ 2015 Runtime for Universal Windows Platform Apps** estensioni per il progetto UWP.

## <a name="initializing-the-local-store"></a>L'inizializzazione di Store locale

Per poter eseguire qualsiasi operazione di tabella di sincronizzazione, è necessario inizializzare l'archivio locale. Questa operazione viene eseguita nel progetto libreria di classi portabile (PCL) della soluzione xamarin. Forms:

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

Viene creato un nuovo database SQLite locale per il `MobileServiceSQLiteStore` classe, a condizione che non esiste già. Successivamente, il `DefineTable<T>` metodo crea una tabella nell'archivio locale corrispondente ai campi nel `TodoItem` digitare, a condizione che non esiste già.

Oggetto *contesto di sincronizzazione* è associato un `MobileServiceClient` istanza e tiene traccia delle modifiche apportate con le tabelle di sincronizzazione. Il contesto di sincronizzazione gestisce una coda che mantiene un elenco ordinato di operazioni di creazione, aggiornamento ed eliminazione (CUD) che verrà inviata all'istanza di App per dispositivi mobili di Azure in un secondo momento. Il `IMobileServiceSyncContext.InitializeAsync()` metodo viene utilizzato per associare l'archivio locale con il contesto di sincronizzazione.

Il `todoTable` campo è un `IMobileServiceSyncTable`, e pertanto tutte le operazioni CRUD usano l'archivio locale.

## <a name="performing-synchronization"></a>Esecuzione della sincronizzazione

L'archivio locale è sincronizzata con l'App per dispositivi mobili di Azure istanza quando la `SyncAsync` metodo viene richiamato:

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

Il `IMobileServiceSyncTable.PushAsync` metodo opera sul contesto di sincronizzazione, anziché una tabella specifica e invia tutte le modifiche CUD apportate dopo l'ultimo push.

Pull viene eseguito mediante il `IMobileServiceSyncTable.PullAsync` metodo su una singola tabella. Il primo parametro per il `PullAsync` metodo è un nome di query che viene usato solo sul dispositivo mobile. Fornire nome viene importata il SDK Client per dispositivi mobili di Azure che esegue una query non null un' *sincronizzazione incrementale*, dove ogni volta che un'operazione pull restituisce risultati, la versione più recente `updatedAt` dai risultati che è archiviato in locale tabelle di sistema. Operazioni pull successive recuperano solo i record successivi a tale timestamp. In alternativa *sincronizzazione completa* può essere ottenuto mediante il passaggio `null` come il nome della query, in modo da tutti i record recuperati in ogni operazione pull. Qualsiasi operazione di sincronizzazione, in seguito ricevuto i dati vengono inseriti nell'archivio locale.

> [!NOTE]
> Se viene effettuato il pull in una tabella con aggiornamenti locali in sospeso, il pull eseguirà prima un'operazione push sul contesto di sincronizzazione. Ciò riduce al minimo i conflitti tra le modifiche già in coda e i nuovi dati dall'istanza di App per dispositivi mobili di Azure.

Il `SyncAsync` metodo include anche un'implementazione di base per la gestione dei conflitti quando lo stesso record viene modificato in entrambi nell'archivio locale e nell'istanza di App per dispositivi mobili di Azure. Quando il conflitto è che i dati sono stati aggiornati nell'archivio locale e nell'istanza di App per dispositivi mobili di Azure, il `SyncAsync` metodo aggiorna i dati nell'archivio locale dai dati archiviati nell'istanza di App per dispositivi mobili di Azure. Quando si verifica alcun conflitto altri, il `SyncAsync` metodo ignora la modifica locale. In grado di gestire lo scenario in cui è presente una modifica locale per i dati che sono stati eliminati dall'istanza di App per dispositivi mobili di Azure.

In un'applicazione di produzione, gli sviluppatori devono scrivere una classe personalizzata `IMobileServiceSyncHandler` implementazione di gestione dei conflitti adatti ai rispettivi casi d'uso. Per altre informazioni, vedere [Usa concorrenza ottimistica per la risoluzione dei conflitti](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) nel portale di Azure, e [avanzato approfondire il supporto offline nel SDK del client gestito](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) sul blog di MSDN.

## <a name="purging-data"></a>Eliminazione dei dati

Le tabelle nell'archivio locale possono essere cancellate i dati con il `IMobileServiceSyncTable.PurgeAsync` (metodo). Questo metodo supporta gli scenari, ad esempio la rimozione dei dati non aggiornati che un'applicazione non è più necessario. Ad esempio, Visualizza solo l'applicazione di esempio `TodoItem` istanze che non è state complete. Di conseguenza, gli elementi completati non più necessario siano archiviati localmente. Eliminazione di elementi completati dall'archivio locale può essere eseguita come indicato di seguito:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Una chiamata a `PurgeAsync` inoltre attiva un'operazione push. Pertanto, tutti gli elementi che sono contrassegnati come completati in locale verranno inviati all'istanza di App per dispositivi mobili di Azure prima di essere rimossa dall'archivio locale. Tuttavia, se sono presenti operazioni in sospeso la sincronizzazione con l'istanza di App per dispositivi mobili di Azure, l'operazione di ripulitura genererà un' `InvalidOperationException` , a meno che il `force` parametro è impostato su `true`. Una strategia alternativa consiste nell'esaminare il `IMobileServiceSyncContext.PendingOperations` proprietà, che restituisce il numero di operazioni che non è stato eseguito il push all'istanza di App per dispositivi mobili di Azure e se la proprietà è uguale a zero solo eseguire l'eliminazione in sospeso.

> [!NOTE]
> Richiamo `PurgeAsync` con il `force` parametro impostato su `true` perderanno le modifiche in sospeso.

## <a name="initiating-synchronization"></a>Avvio della sincronizzazione

Nell'applicazione di esempio, il `SyncAsync` metodo viene richiamato tramite la `TodoList.OnAppearing` metodo:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Ciò significa che l'applicazione tenterà di eseguire la sincronizzazione con l'istanza di App per dispositivi mobili di Azure all'avvio.

Inoltre, sincronizzazione può essere avviata in iOS e Android usando pull per aggiornare l'elenco di dati e nelle piattaforme Windows usando il **sincronizzazione** pulsante nell'interfaccia utente. Per altre informazioni, vedere [Pull all'aggiornamento](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come aggiungere funzionalità di sincronizzazione offline per un'applicazione xamarin. Forms. La sincronizzazione offline consente agli utenti di interagire con un'applicazione per dispositivi mobili, visualizzazione, aggiunta o modifica dei dati, anche se non c'è una connessione di rete. Le modifiche vengono archiviate in un database locale e quando il dispositivo è online, le modifiche possono essere sincronizzate con l'istanza di App per dispositivi mobili di Azure.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuthOfflineSync (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticazione degli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
