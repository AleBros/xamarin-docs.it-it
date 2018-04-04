---
title: Servizi avviati con xamarin
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: c0aeeaad3798dd840e69c6da6d7857298f4da3c1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="started-services-with-xamarinandroid"></a>Servizi avviati con xamarin

## <a name="started-services-overview"></a>Panoramica di servizi avviati

Servizi avviati in genere eseguono un'unità di lavoro senza fornire un feedback diretto o i risultati al client. Un esempio di un'unità di lavoro è un servizio che consente di caricare un file di un server. Il client verrà effettuare una richiesta a un servizio per caricare un file dal dispositivo a un sito Web. Il servizio caricherà il file in modalità non interattiva (anche se l'app non sono presenti attività in primo piano) e interrompe l'esecuzione quando viene completato il caricamento. È importante tenere presente che un servizio avviato verrà eseguito sul thread dell'interfaccia utente di un'applicazione. Ciò significa che se un servizio per eseguire operazioni che bloccano il thread dell'interfaccia utente, è necessario creare ed eliminare dei thread in base alle esigenze.

A differenza di un servizio associato, non è presente alcun canale di comunicazione tra un servizio avviato "puro" e i relativi client. Ciò significa che un servizio avviato implementerà alcuni metodi del ciclo di vita diverso rispetto a un servizio associato. Nell'elenco seguente illustra i metodi del ciclo di vita in un servizio avviato:

* `OnCreate` &ndash; Chiamato quando il servizio di primo avvio di una volta. Si tratta in cui il codice di inizializzazione deve essere implementato.
* `OnBind` &ndash; Questo metodo deve essere implementato da tutte le classi di servizio, tuttavia un servizio avviato in genere non ha un client a esso associato. Per questo motivo, un servizio avviato restituisce semplicemente `null`. Al contrario, un servizio ibrido (che è la combinazione di un servizio associato e un servizio avviato) ha implementare e restituire un `Binder` per il client.
* `OnStartCommand` &ndash; Chiamato per ogni richiesta per avviare il servizio, in risposta a una chiamata a `StartService` o il riavvio dal sistema. Si tratta in cui il servizio può iniziare qualsiasi attività a esecuzione prolungata. Il metodo restituisce un `StartCommandResult` valore che indica la modalità o se il sistema deve gestire il riavvio del servizio dopo un arresto a causa di memoria insufficiente. Questa chiamata viene eseguita nel thread principale. Questo metodo viene descritto in dettaglio più avanti.
* `OnDestroy` &ndash; Questo metodo viene chiamato quando il servizio viene eliminata definitivamente. Viene utilizzato per eseguire qualsiasi finale pulizia necessaria.

Il metodo per un servizio avviato importante è il `OnStartCommand` metodo. Verrà richiamato ogni volta che il servizio riceve una richiesta per eseguire alcune operazioni. Frammento di codice seguente è riportato un esempio di `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

Il primo parametro è un `Intent` oggetto contenente i metadati relativi il lavoro da eseguire. Il secondo parametro contiene un `StartCommandFlags` valore che fornisce alcune informazioni sulla chiamata al metodo. Questo parametro è uno dei due valori possibili:

* `StartCommandFlag.Redelivery` &ndash; Ciò significa che il `Intent` ri-consegna precedente di `Intent`. Questo valore viene fornito quando il servizio ha restituito `StartCommandResult.RedeliverIntent` ma è stato arrestato prima che può essere arrestato correttamente.
* `StartCommandFlag.Retry` &dash; Questo valore viene visualizzato quando una precedente `OnStartCommand` chiamata non riuscita e Android tenta di riavviare il servizio con lo stesso scopo come il precedente tentativo non riuscito.
 
Infine, il terzo parametro è un valore intero che è univoco per l'applicazione che identifica la richiesta. È possibile che i chiamanti più possono richiamare lo stesso oggetto servizio. Questo valore viene utilizzato per associare una richiesta per arrestare un servizio con una determinata richiesta per avviare un servizio. Verrà illustrata più dettagliatamente nella sezione [l'arresto del servizio](#Stopping_the_Service). 

Il valore `StartCommandResult` viene restituito dal servizio come un suggerimento per Android sulle operazioni da eseguire se il servizio viene terminato a causa dei vincoli di risorse. Esistono tre possibili valori per `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; questo valore indica Android che non è necessario riavviare il servizio che è terminato. Ad esempio di questo, prendere in considerazione un servizio che genera l'errore anteprime per una raccolta in un'applicazione. Se il servizio viene terminato, non è fondamentale per ricreare l'anteprima immediatamente &ndash; l'anteprima può essere creata nuovamente alla successiva esecuzione dell'app.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; indica Android per riavviare il servizio, ma non per recapitare la finalità dell'ultimo che ha avviato il servizio. Se non esistono alcun intenti in sospeso per gestire, quindi un `null` verrà fornito per il parametro finalità. Un esempio di questo potrebbe essere un'app di Windows Media player musica; il servizio verrà riavviato pronto per riprodurre musica, ma verrà riprodotto il brano ultimo. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; questo valore è indicare Android per riavviare il servizio e inviare di nuovo l'ultima `Intent`. Un esempio di questo è un servizio che consente di scaricare un file di dati per un'app. Se il servizio viene terminato, il file di dati ancora dovrà essere scaricati. Restituendo `StartCommandResult.RedeliverIntent`quando Android riavvia il servizio fornirà anche la finalità (che contiene l'URL del file da scaricare) al servizio. In questo modo il download riavviare oppure riprendere (a seconda dell'implementazione esatta del codice).

È un quarto valore per `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Questo valore viene restituito da `OnStartCommand` e descrive come Android continuerà il servizio è terminato. Questo valore non è in genere utilizzato per avviare un servizio.

In questo diagramma sono riportati gli eventi del ciclo di vita di chiavi di un servizio avviato 

![Un diagramma che illustra l'ordine in cui vengono chiamati i metodi del ciclo di vita](started-services-images/started-service-01.png "un diagramma che illustra l'ordine in cui vengono chiamati i metodi del ciclo di vita.")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>L'arresto del servizio

Un servizio avviato continueranno a essere eseguite indefinitamente. Android consente di mantenere il servizio in esecuzione fino a quando sono disponibili risorse di sistema sufficienti. Il client è necessario arrestare il servizio o il servizio può arrestare se stesso al termine del lavoro. Esistono due modi per arrestare un servizio: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; un client (ad esempio un'attività) può richiedere un servizio venga arrestato la chiamata di `StopService` metodo: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; un servizio può arrestare stesso richiamando il `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>Utilizza startId per arrestare un servizio

Più i chiamanti possono richiedere l'avvio di un servizio. Se è presente una richiesta di avvio in sospeso, è possibile utilizzare il servizio di `startId` che viene passata `OnStartCommand` per impedire che il servizio viene interrotto in modo anomalo. Il `startId` corrisponderà alla chiamata più recente a `StartService`e viene incrementato ogni volta che viene chiamato. Pertanto, se una richiesta successiva per `StartService` ha non sono ancora state una chiamata a `OnStartCommand`, il servizio può chiamare `StopSelfResult`, passandogli il valore più recente di `startId` ha ricevuto (anziché semplicemente chiamare `StopSelf`). Se una chiamata a `StartService` ha non sono ancora state una corrispondente chiamata a `OnStartCommand`, il sistema non interrompe il servizio, in quanto il `startId` utilizzato nel `StopSelf` non corrispondono all'ultima chiamata `StartService` chiamare.


## <a name="related-links"></a>Collegamenti correlati

- [StartedServicesDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [Icone della barra di stato](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
