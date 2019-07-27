---
title: Finalità dei servizi in Novell. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 4c868623ae08ac1366c1c9ea55c8d635f0a6a061
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509133"
---
# <a name="intent-services-in-xamarinandroid"></a>Finalità dei servizi in Novell. Android

## <a name="intent-services-overview"></a>Panoramica di Intent Services

Sia i servizi avviati che i servizi associati vengono eseguiti sul thread principale, il che significa che per garantire prestazioni ottimali, un servizio deve eseguire il lavoro in modo asincrono. Uno dei modi più semplici per risolvere questo problema è il modello di _processore della coda di lavoro_, in cui il lavoro da eseguire viene inserito in una coda che viene gestita da un singolo thread.

È una sottoclasse `Service` della classe che fornisce un'implementazione specifica di Android di questo modello. [`IntentService`](xref:Android.App.IntentService) Verranno gestite le operazioni di Accodamento, l'avvio di un thread di lavoro per il servizio della coda e il pull delle richieste dalla coda per l'esecuzione nel thread di lavoro. Un `IntentService` si arresterà in modo silenzioso e rimuoverà il thread di lavoro quando la coda non è più disponibile.

Il lavoro viene inviato alla coda creando un oggetto `Intent` e quindi `Intent` passandolo al `StartService` metodo.

Non è possibile arrestare o interrompere il `OnHandleIntent` metodo `IntentService` mentre è funzionante. A causa di questa progettazione, `IntentService` un deve &ndash; rimanere senza stato, non deve basarsi su una connessione attiva o sulla comunicazione dal resto dell'applicazione. Un `IntentService` è concepito per elaborare le richieste di lavoro senza stato.

Esistono due requisiti per la sottoclasse `IntentService`:

1. Il nuovo tipo (creato dalla sottoclasse `IntentService`) esegue l'override solo del `OnHandleIntent` metodo.
2. Il costruttore per il nuovo tipo richiede una stringa che viene usata per assegnare un nome al thread di lavoro che gestirà le richieste. Il nome di questo thread di lavoro viene utilizzato principalmente durante il debug dell'applicazione.

Nel codice seguente viene illustrata un' `IntentService` implementazione di con il metodo sottoposto a override: `OnHandleIntent`

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

Il lavoro viene inviato a `IntentService` un oggetto creando un' `Intent` istanza di e quindi [`StartService`](xref:Android.Content.Context.StartService*) chiamando il metodo con tale scopo come parametro. Lo scopo verrà passato al servizio come parametro nel `OnHandleIntent` metodo. Questo frammento di codice è un esempio di invio di una richiesta di lavoro a un preventivo: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService` Può estrarre i valori dallo scopo, come illustrato in questo frammento di codice:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");

    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```

## <a name="related-links"></a>Collegamenti correlati

- [IntentService](xref:Android.App.IntentService)
- [StartService](xref:Android.Content.Context.StartService*)
