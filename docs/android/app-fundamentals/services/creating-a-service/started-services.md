---
title: Servizi avviati con xamarin. Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df3d1bba57c1caf23c615410a184bc5458fc848b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671351"
---
# <a name="started-services-with-xamarinandroid"></a>Servizi avviati con xamarin. Android

## <a name="started-services-overview"></a>Panoramica di servizi avviati

Servizi avviati eseguono in genere un'unità di lavoro senza fornire commenti diretti o dei risultati al client. Un esempio di un'unità di lavoro è un servizio che carica un file in un server. Il client verrà effettuare una richiesta a un servizio per caricare un file dal dispositivo a un sito Web. Il servizio caricherà il file in modalità non interattiva (anche se l'app è associata alcuna attività in primo piano) e terminare se stessa quando viene completato il caricamento. È importante tenere presente che un servizio avviato verrà eseguito sul thread dell'interfaccia utente di un'applicazione. Ciò significa che se un servizio deve eseguire le operazioni che bloccano il thread dell'interfaccia utente, è necessario creare ed eliminare dei thread in base alle esigenze.

A differenza di un servizio associato, non vi è alcun canale di comunicazione tra un servizio avviato "puro" e i relativi client. Ciò significa che un servizio avviato implementerà alcuni metodi del ciclo di vita diverso rispetto a un servizio associato. Nell'elenco seguente illustra i metodi del ciclo di vita comune in un servizio avviato:

* `OnCreate` &ndash; Chiamato una volta al primo avvio al servizio. Si tratta in cui il codice di inizializzazione deve essere implementato.
* `OnBind` &ndash; Questo metodo deve essere implementato da tutte le classi di servizio, tuttavia un servizio avviato tipicamente non hanno un client a esso associati. Per questo motivo, un servizio avviato restituisce semplicemente `null`. Al contrario, un servizio ibrido, ovvero la combinazione di un servizio associato e un servizio avviato, dispone implementare e restituire un `Binder` per il client.
* `OnStartCommand` &ndash; Chiamato per ogni richiesta per avviare il servizio, in risposta a una chiamata a `StartService` o il riavvio dal sistema. Si tratta in cui il servizio può iniziare qualsiasi attività a esecuzione prolungata. Il metodo restituisce un `StartCommandResult` valore che indica come o se il sistema deve gestire il riavvio del servizio dopo un arresto a causa di memoria insufficiente. Questa chiamata viene eseguita sul thread principale. Questo metodo viene descritto in dettaglio più avanti.
* `OnDestroy` &ndash; Questo metodo viene chiamato quando il servizio viene eliminata definitivamente. Viene usato per eseguire qualsiasi finale pulizia necessaria.

Il metodo importante per un servizio avviato è il `OnStartCommand` (metodo). Verrà richiamato ogni volta che il servizio riceve una richiesta di eseguire alcune operazioni. Il frammento di codice seguente è riportato un esempio di `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

Il primo parametro è un `Intent` oggetto contenente i metadati sulle operazioni da eseguire. Il secondo parametro contiene un `StartCommandFlags` valore che fornisce alcune informazioni sulla chiamata al metodo. Questo parametro è uno dei due valori possibili:

* `StartCommandFlag.Redelivery` &ndash; Ciò significa che il `Intent` è un re-recapito di una precedente `Intent`. Questo valore viene fornito quando il servizio ha restituito `StartCommandResult.RedeliverIntent` ma è stata interrotta prima che può essere arrestato correttamente.
* `StartCommandFlag.Retry` &dash; Questo valore viene ricevuto quando una precedente `OnStartCommand` chiamata non riuscita e Android sta tentando di avviare nuovamente il servizio con lo stesso scopo come il precedente tentativo non riuscito.
 
Infine, il terzo parametro è un valore integer univoco per l'applicazione che identifica la richiesta. È possibile che i chiamanti più possono richiamare lo stesso oggetto servizio. Questo valore viene utilizzato per associare una richiesta per arrestare un servizio con una determinata richiesta per avviare un servizio. Verrà illustrata più dettagliatamente nella sezione [arrestando il servizio](#Stopping_the_Service). 

Il valore `StartCommandResult` viene restituito dal servizio come un suggerimento per Android sulle operazioni da eseguire se il servizio viene terminato a causa dei vincoli di risorse. Esistono tre possibili valori per `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; questo valore indica a Android che non è necessario riavviare il servizio che è terminato. Ad esempio di questo, si consideri un servizio che genera le anteprime per una raccolta in un'app. Se il servizio viene terminato, non è fondamentale per ricreare l'anteprima immediatamente &ndash; l'anteprima può essere ricreato alla successiva esecuzione dell'app.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; si chiede ad Android per riavviare il servizio, ma non per offrire l'ultimo Intent che ha avviato il servizio. Se non esistono alcuna finalità in sospeso per gestire, quindi un `null` verrà fornito per il parametro Intent. Un esempio di questo potrebbe essere un'app lettore musicale; il servizio verrà riavviato pronto per la riproduzione di brani musicali, ma verrà riprodotta l'ultimo brano. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; questo valore è will comunicare ad Android per riavviare il servizio e inviare di nuovo l'ultimo `Intent`. Un esempio di questo è un servizio che consente di scaricare un file di dati per un'app. Se il servizio viene terminato, il file di dati deve ancora essere scaricato. Restituendo `StartCommandResult.RedeliverIntent`quando Android riavvia il servizio fornirà anche la finalità (che contiene l'URL del file da scaricare) al servizio. Ciò consentirà il download riavviare o riprendere (a seconda dell'implementazione esatta del codice).

È disponibile un quarto valore per `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Questo valore viene restituito da `OnStartCommand` e descrive come Android continuerà il servizio è terminato. Questo valore non è in genere usato per avviare un servizio.

In questo diagramma vengono visualizzati gli eventi del ciclo di vita di chiavi di un servizio avviato: 

![Diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita](started-services-images/started-service-01.png "diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita.")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>L'arresto del servizio

Un servizio avviato esecuzione continuerà indefinitamente. Android consente di mantenere il servizio in esecuzione fino a quando sono disponibili risorse di sistema sufficienti. Il client è necessario arrestare il servizio o il servizio può arrestare se stesso al termine il proprio lavoro. Esistono due modi per arrestare un servizio: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; un client (ad esempio, un'attività) può richiedere un servizio venga arrestato la chiamata di `StopService` metodo: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; un servizio potrebbe arrestarsi richiamando il `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>Utilizza startId per arrestare un servizio

Ai chiamanti più possono richiedere che un servizio di essere avviato. Se è presente una richiesta di avvio in sospeso, il servizio può usare la `startId` che viene passata `OnStartCommand` per impedire che il servizio viene interrotto in modo anomalo. Il `startId` corrisponderà alla chiamata più recente a `StartService`e viene incrementato ogni volta che viene chiamato. Pertanto, se una richiesta successiva al `StartService` non hanno ancora in una chiamata a `OnStartCommand`, il servizio può chiamare `StopSelfResult`, passandogli il valore più recente del `startId` ha ricevuto (anziché semplicemente chiamare `StopSelf`). Se una chiamata a `StartService` non hanno ancora in una chiamata corrispondente a `OnStartCommand`, il sistema non arresterà il servizio, in quanto il `startId` usato nel `StopSelf` chiamata non corrisponderà alla versione più recente `StartService` chiamare.


## <a name="related-links"></a>Collegamenti correlati

- [StartedServicesDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [Icone della barra di stato](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
