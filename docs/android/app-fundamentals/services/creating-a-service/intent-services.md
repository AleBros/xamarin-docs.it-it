---
title: Servizi preventivi in xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 7d8f77ac4da9eb992d0a2be7e623f1e33d57b99c
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="intent-services-in-xamarinandroid"></a>Servizi preventivi in xamarin

## <a name="intent-services-overview"></a>Panoramica di servizi preventivo

Sia avviato e vengono eseguiti nel thread principale, il che significa che per mantenere prestazioni smooth, un servizio deve eseguire il lavoro in modo asincrono i servizi associati. Uno dei modi più semplici per risolvere questo problema è con un _modello processore di coda lavoro_, in cui il lavoro da eseguire viene inserito in una coda che viene gestita da un singolo thread. 

Il [ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) è una sottoclasse di `Service` classe che fornisce un'implementazione specifica Android di questo modello. Da gestire operazioni di accodamento, avvio di un thread di lavoro per gestire la coda, e le richieste pull disattivata la coda da eseguire sul thread di lavoro. Un `IntentService` interrompe stesso e rimuovere il thread di lavoro quando sono presenti operazioni non sono più presenti nella coda in modalità non interattiva.
 
Il lavoro viene inviato alla coda mediante la creazione di un `Intent` e quindi passando il `Intent` per il `StartService` metodo.

Non è possibile arrestare o interrompere il `OnHandleIntent` metodo `IntentService` durante il lavoro. Per questa ragione, un `IntentService` deve rimanere senza stato &ndash; non deve basarsi su una connessione attiva o la comunicazione dal resto dell'applicazione. Un `IntentService` intende statelessly elaborare le richieste di lavoro.

Esistono due requisiti per la creazione di sottoclassi `IntentService`:

1. Il nuovo tipo (creato dalla creazione di una sottoclasse `IntentService`) esegue l'override solo di `OnHandleIntent` metodo.
2. Il costruttore per il nuovo tipo richiede una stringa che viene utilizzata per denominare il thread di lavoro che gestirà le richieste. Il nome del thread di lavoro viene utilizzato principalmente per il debug dell'applicazione.

Il codice seguente illustra un `IntentService` implementazione con sottoposto a override `OnHandleIntent` metodo:

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

Lavoro viene inviato a un `IntentService` creando un `Intent` e chiamando quindi il [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) metodo con tale intento come parametro. Lo scopo verrà passato come parametro in per il servizio di `OnHandleIntent` metodo. Questo frammento di codice è riportato un esempio di inviare una richiesta di lavoro a un intento di: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

Il `IntentService` possibile estrarre i valori da finalità, come illustrato nel frammento di codice seguente:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>Collegamenti correlati

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
