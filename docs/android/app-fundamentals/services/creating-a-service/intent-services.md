---
title: Finalità dei servizi in Xamarin.Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: acf8824c7a575bca37301a409bdf6d5f42cca622
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488062"
---
# <a name="intent-services-in-xamarinandroid"></a>Finalità dei servizi in Xamarin.Android

Sia i servizi avviati che i servizi associati vengono eseguiti sul thread principale, il che significa che per garantire prestazioni ottimali, un servizio deve eseguire il lavoro in modo asincrono. Uno dei modi più semplici per risolvere questo problema è il modello di _processore della coda di lavoro_, in cui il lavoro da eseguire viene inserito in una coda che viene gestita da un singolo thread.

Il [`IntentService`](xref:Android.App.IntentService) è una sottoclasse della classe `Service` che fornisce un'implementazione specifica di Android di questo modello. Verranno gestite le operazioni di Accodamento, l'avvio di un thread di lavoro per il servizio della coda e il pull delle richieste dalla coda per l'esecuzione nel thread di lavoro. Un `IntentService` si arresterà in modo silenzioso e rimuoverà il thread di lavoro quando la coda non è più disponibile.

Il lavoro viene inviato alla coda creando una `Intent` e quindi passando tale `Intent` al metodo `StartService`.

Non è possibile arrestare o interrompere il metodo `OnHandleIntent` `IntentService` mentre è funzionante. A causa di questa progettazione, un `IntentService` deve rimanere senza stato &ndash; non deve basarsi su una connessione attiva o sulla comunicazione dal resto dell'applicazione. Un `IntentService` è concepito per elaborare le richieste di lavoro senza stato.

Esistono due requisiti per la sottoclasse `IntentService`:

1. Il nuovo tipo (creato dalla sottoclasse `IntentService`) esegue l'override solo del metodo `OnHandleIntent`.
2. Il costruttore per il nuovo tipo richiede una stringa che viene usata per assegnare un nome al thread di lavoro che gestirà le richieste. Il nome di questo thread di lavoro viene utilizzato principalmente durante il debug dell'applicazione.

Nel codice seguente viene illustrata un'implementazione di `IntentService` con il metodo `OnHandleIntent` sottoposto a override:

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

Il lavoro viene inviato a un `IntentService` creando un'istanza di un `Intent` e quindi chiamando il metodo [`StartService`](xref:Android.Content.Context.StartService*) con tale finalità come parametro. Lo scopo verrà passato al servizio come parametro nel metodo `OnHandleIntent`. Questo frammento di codice è un esempio di invio di una richiesta di lavoro a un preventivo: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.PutPutExtra("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

Il `IntentService` può estrarre i valori dallo scopo, come illustrato nel frammento di codice seguente:  

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
