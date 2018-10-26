---
title: Servizi Intent in xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1301f34ad1f7a0069c542ba81bf237a673fd239d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112858"
---
# <a name="intent-services-in-xamarinandroid"></a>Servizi Intent in xamarin. Android

## <a name="intent-services-overview"></a>Panoramica di servizi Intent

Sia avviato e associato vengono eseguiti nel thread principale, il che significa che, per mantenere le prestazioni uniformi, un servizio deve eseguire un'operazione in modo asincrono i servizi. Uno dei modi più semplici per risolvere questo problema è con un _modello di ruolo di lavoro coda processore_, in cui il lavoro da eseguire viene posizionato in una coda che viene gestita da un singolo thread. 

Il [ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) è una sottoclasse del `Service` classe che fornisce un'implementazione specifica Android di questo modello. Da gestire lavoro queueing, avvio di un thread di lavoro per la coda del servizio e le richieste pull disattivata la coda per essere eseguito nel thread di lavoro. Un `IntentService` sarà in modalità non interattiva arrestare se stesso e rimuovere il thread di lavoro quando sono presenti operazioni non è più in coda.
 
Il lavoro viene inviato alla coda tramite la creazione di un' `Intent` e quindi passandolo `Intent` per il `StartService` (metodo).

Non è possibile arrestare o interrompere il `OnHandleIntent` metodo `IntentService` durante il lavoro. A causa di questa struttura, un' `IntentService` deve essere mantenuto senza stato &ndash; non deve basarsi su una connessione attiva o la comunicazione dal resto dell'applicazione. Un `IntentService` è destinato a statelessly elaborare le richieste di lavoro.

Esistono due requisiti per la creazione di una sottoclasse `IntentService`:

1. Il nuovo tipo (creata creando sottoclassi `IntentService`) consente di ignorare solo i `OnHandleIntent` (metodo).
2. Il costruttore per il nuovo tipo richiede una stringa che viene usata per denominare il thread di lavoro che gestirà le richieste. Il nome del thread di lavoro viene utilizzato principalmente per il debug dell'applicazione.

Il codice seguente illustra un' `IntentService` implementazione con sottoposto a override `OnHandleIntent` metodo:

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }
    
    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

Lavoro viene inviato a un `IntentService` creando un `Intent` e chiamando quindi il [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) metodo con tale intento come parametro. La finalità verrà passata al servizio come parametro nel `OnHandleIntent` (metodo). Questo frammento di codice è un esempio di inviare una richiesta di lavoro a un Intent: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

Il `IntentService` può estrarre i valori dall'intenzione, come illustrato nel frammento di codice seguente:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>Collegamenti correlati

- [Elemento IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
