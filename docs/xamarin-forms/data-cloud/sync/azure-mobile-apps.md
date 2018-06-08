---
title: La sincronizzazione dei dati non in linea con le App per dispositivi mobili di Azure
description: Sincronizzazione non in linea consente agli utenti di interagire con un'applicazione per dispositivi mobili, visualizzazione, aggiunta o modifica dei dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale e una volta che il dispositivo è online, le modifiche possono essere sincronizzate con l'istanza di App mobili di Azure. In questo articolo viene illustrato come aggiungere la funzionalità di sincronizzazione non in linea a un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 8623127444836d3335f42f9ba7a40de6aedfef70
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848369"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>La sincronizzazione dei dati non in linea con le App per dispositivi mobili di Azure

_Sincronizzazione non in linea consente agli utenti di interagire con un'applicazione per dispositivi mobili, visualizzazione, aggiunta o modifica dei dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale e una volta che il dispositivo è online, le modifiche possono essere sincronizzate con l'istanza di App mobili di Azure. In questo articolo viene illustrato come aggiungere la funzionalità di sincronizzazione non in linea a un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

Il [Client mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornisce il `IMobileServiceTable` interfaccia che rappresenta le operazioni che possono essere eseguite nelle tabelle archiviate nell'istanza di App mobili di Azure. Queste operazioni connettono direttamente all'istanza di App mobili di Azure e avrà esito negativo se il dispositivo mobile non dispone di una connessione di rete.

Per supportare la sincronizzazione non in linea, il Client di dispositivi mobili di Azure SDK supporta *sincronizzare tabelle*, che vengono forniti per il `IMobileServiceSyncTable` interfaccia. Questa interfaccia fornisce la stessa creazione, lettura, aggiornamento, le operazioni di eliminazione (CRUD) come il `IMobileServiceTable` interfaccia, ma le operazioni di lettura o scrittura in un archivio locale. L'archivio locale non è popolato con i nuovi dati dall'istanza di App mobili di Azure fino a quando non vi è una chiamata a *pull* dati. Analogamente, dati non viene inviati all'istanza di App mobili di Azure fino a quando non vi è una chiamata a *push* modifiche locali.

Sincronizzazione non in linea include inoltre il supporto per il rilevamento dei conflitti quando lo stesso record viene modificato in entrambi l'archivio locale e nell'istanza di App mobili di Azure e la risoluzione dei conflitti personalizzato. Può essere gestito è in conflitto nell'archivio locale o nell'istanza di App mobili di Azure.

Per ulteriori informazioni sulla sincronizzazione offline, vedere [sincronizzazione dati Offline nelle App mobili di Azure](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) e [abilitare la sincronizzazione non in linea per l'app per dispositivi mobili con xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di sincronizzazione non in linea tra un'applicazione di xamarin. Forms e un'istanza di App mobili di Azure è come segue:

1. Creare un'istanza di App mobili di Azure. Per ulteriori informazioni, vedere [utilizzo di un'App Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Aggiungere il [Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) pacchetto NuGet per tutti i progetti nella soluzione xamarin. Forms.
1. (Facoltativo) Abilitare l'autenticazione nell'istanza App mobili di Azure e l'applicazione di xamarin. Forms. Per ulteriori informazioni, vedere [autenticazione degli utenti con App mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

Nella sezione seguente fornisce le istruzioni di configurazione aggiuntive per la configurazione dei progetti di piattaforma UWP (Universal Windows) per usare il pacchetto Microsoft.Azure.Mobile.Client.SQLiteStore NuGet. Nessuna configurazione aggiuntiva è necessario utilizzare il pacchetto Microsoft.Azure.Mobile.Client.SQLiteStore NuGet in iOS e Android.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Per utilizzare SQLite sulla piattaforma UWP (Universal Windows), seguire questi passaggi:

1. Installare il [SQLite per la piattaforma Windows universale](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) estensione nell'ambiente di sviluppo di Visual Studio.
1. Nel progetto UWP in Visual Studio, fare clic **riferimenti > Aggiungi riferimento**, passare a **estensioni** e aggiungere il **SQLite per la piattaforma UWP** e  **Runtime di Visual C++ 2015 per App di Windows universale** estensioni per il progetto UWP.

## <a name="initializing-the-local-store"></a>Inizializzazione dell'archivio locale

È necessario inizializzare l'archivio locale prima di possono eseguire qualsiasi operazione di tabella di sincronizzazione. Questa operazione viene eseguita nel progetto libreria di classe portabile (PCL) della soluzione xamarin. Forms:

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

Creando un nuovo database locale di SQLite la `MobileServiceSQLiteStore` classe, a condizione che non esiste già. Quindi, `DefineTable<T>` metodo crea una tabella nell'archivio locale che soddisfi i campi di `TodoItem` digitare, a condizione che non esiste già.

Oggetto *contesto di sincronizzazione* è associato un `MobileServiceClient` istanza e tiene traccia delle modifiche apportate con le tabelle della sincronizzazione. Il contesto di sincronizzazione gestisce una coda che mantiene un elenco ordinato di operazioni Create, Update e Delete (CUD) che verrà inviata all'istanza di App mobili di Azure in un secondo momento. Il `IMobileServiceSyncContext.InitializeAsync()` metodo viene utilizzato per associare l'archivio locale con il contesto di sincronizzazione.

Il `todoTable` campo è un `IMobileServiceSyncTable`, e tutte le operazioni CRUD utilizzano l'archivio locale.

## <a name="performing-synchronization"></a>Esecuzione della sincronizzazione

L'archivio locale è sincronizzata con l'App per dispositivi mobili Azure istanza quando la `SyncAsync` metodo viene richiamato:

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

Il `IMobileServiceSyncTable.PushAsync` metodo opera nel contesto di sincronizzazione, anziché una tabella specifica e invia tutte le modifiche CUD dopo l'ultimo comando.

Pull viene eseguito mediante il `IMobileServiceSyncTable.PullAsync` metodo su una singola tabella. Il primo parametro di `PullAsync` metodo è un nome di query che viene utilizzato solo sul dispositivo mobile. Fornire una query non null nome viene importata il SDK Client Mobile di Azure che esegue un *sincronizzazione incrementale*, in cui ogni volta che un'operazione pull restituisce risultati, la versione più aggiornata `updatedAt` dai risultati è archiviato in locale tabelle di sistema. Operazioni successive pull recuperano solo i record dopo tale timestamp. In alternativa, *sincronizzazione completa* può essere ottenuto passando `null` come il nome della query, che restituisce tutti i record da recuperare in ogni operazione di pull. Dopo qualsiasi operazione di sincronizzazione, i dati ricevuti viene inseriti nell'archivio locale.

> [!NOTE]
> Se un'operazione di pull viene eseguita su una tabella che include aggiornamenti locali in sospeso, il pull eseguirà innanzitutto un push nel contesto di sincronizzazione. Questo riduce al minimo i conflitti tra le modifiche che sono già in coda e i nuovi dati dall'istanza di App mobili di Azure.

Il `SyncAsync` metodo include anche un'implementazione di base per gestire i conflitti quando lo stesso record viene modificato in entrambi l'archivio locale e nell'istanza di App mobili di Azure. Quando il conflitto è che i dati sono stati aggiornati nell'archivio locale e nell'istanza di App mobili di Azure, il `SyncAsync` metodo aggiorna i dati nell'archivio locale dai dati archiviati nell'istanza di App mobili di Azure. Quando si verifica eventuali conflitti con altri, il `SyncAsync` metodo ignora la modifica locale. In grado di gestire lo scenario in cui è presente una modifica locale per i dati che sono stati eliminati dall'istanza di App mobili di Azure.

In un'applicazione di produzione, gli sviluppatori devono scrivere un oggetto personalizzato `IMobileServiceSyncHandler` implementazione di gestione dei conflitti è adatto per il caso d'uso. Per ulteriori informazioni, vedere [Usa concorrenza ottimistica per la risoluzione dei conflitti](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) nel portale di Azure e [Deep informazioni sul supporto offline nel client gestito SDK](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) nei blog di MSDN.

## <a name="purging-data"></a>Eliminazione dei dati

È possibile cancellare le tabelle nell'archivio locale dei dati con il `IMobileServiceSyncTable.PurgeAsync` metodo. Questo metodo supporta scenari, ad esempio la rimozione di dati non aggiornati che un'applicazione non è più necessario. Ad esempio, l'applicazione di esempio vengono visualizzati solo `TodoItem` istanze che non sono state complete. Di conseguenza, gli elementi completati non più necessario siano archiviati localmente. Eliminazione di elementi completati dall'archivio locale può essere eseguito come segue:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Una chiamata a `PurgeAsync` attiva anche un'operazione push. Pertanto, tutti gli elementi che sono contrassegnati come completati localmente riceverà all'istanza di App mobili di Azure prima di essere rimossa dall'archivio locale. Tuttavia, se sono presenti operazioni in sospeso la sincronizzazione con l'istanza di App mobili di Azure, la ripulitura genererà un `InvalidOperationException` , a meno che il `force` parametro è impostato su `true`. Una strategia alternativa consiste nell'esaminare il `IMobileServiceSyncContext.PendingOperations` proprietà, che restituisce il numero di operazioni che non sono stati inseriti nell'istanza di App mobili di Azure ed eseguire solo l'eliminazione se la proprietà è zero in sospeso.

> [!NOTE]
> Richiamare `PurgeAsync` con il `force` parametro impostato su `true` perderanno le modifiche in sospeso.

## <a name="initiating-synchronization"></a>Avvio di sincronizzazione

Nell'applicazione di esempio, il `SyncAsync` metodo viene richiamato tramite il `TodoList.OnAppearing` metodo:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Ciò significa che l'applicazione tenterà di eseguire la sincronizzazione con l'istanza di App mobili di Azure all'avvio.

Inoltre, la sincronizzazione può essere avviata in iOS e Android utilizzando pull per aggiornare l'elenco di dati e le piattaforme Windows utilizzando il **sincronizzazione** pulsante nell'interfaccia utente. Per ulteriori informazioni, vedere [Pull all'aggiornamento](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come aggiungere la funzionalità di sincronizzazione non in linea a un'applicazione di xamarin. Forms. Sincronizzazione non in linea consente agli utenti di interagire con un'applicazione per dispositivi mobili, visualizzazione, aggiunta o modifica dei dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale e una volta che il dispositivo è online, le modifiche possono essere sincronizzate con l'istanza di App mobili di Azure.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuthOfflineSync (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [L'autenticazione degli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Client per dispositivi mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
