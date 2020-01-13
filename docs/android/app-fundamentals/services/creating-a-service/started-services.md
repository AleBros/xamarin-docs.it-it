---
title: Avvio dei servizi con Xamarin.Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: f5b5f8cf224c18852a0a0e7e4f591b49905ba026
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024770"
---
# <a name="started-services-with-xamarinandroid"></a>Avvio dei servizi con Xamarin.Android

## <a name="started-services-overview"></a>Panoramica dei servizi avviati

I servizi avviati eseguono in genere un'unità di lavoro senza fornire feedback diretto o risultati al client. Un esempio di unità di lavoro è un servizio che carica un file in un server. Il client effettuerà una richiesta a un servizio per caricare un file dal dispositivo a un sito Web. Il servizio caricherà tranquillamente il file (anche se l'app non ha attività in primo piano) e termina se stessa al termine del caricamento. È importante tenere presente che un servizio avviato verrà eseguito sul thread UI di un'applicazione. Ciò significa che se un servizio è in grado di eseguire operazioni che bloccano il thread dell'interfaccia utente, deve creare ed eliminare i thread in modo necessario.

A differenza di un servizio associato, non esiste alcun canale di comunicazione tra un servizio avviato "pure" e i relativi client. Questo significa che un servizio avviato implementerà alcuni metodi del ciclo di vita diversi rispetto a un servizio associato. Nell'elenco seguente vengono evidenziati i metodi del ciclo di vita comuni in un servizio avviato:

- `OnCreate` &ndash; chiamato una volta quando il servizio viene avviato per la prima volta. Questo è il punto in cui deve essere implementato il codice di inizializzazione.
- `OnBind` &ndash; questo metodo deve essere implementato da tutte le classi del servizio, tuttavia in un servizio avviato non è in genere associato a un client. Per questo motivo, un servizio avviato restituisce solo `null`. Al contrario, un servizio ibrido (ovvero la combinazione di un servizio associato e di un servizio avviato) deve implementare e restituire un `Binder` per il client.
- `OnStartCommand` &ndash; chiamato per ogni richiesta di avvio del servizio, in risposta a una chiamata a `StartService` o a un riavvio del sistema. Questo è il punto in cui il servizio può iniziare qualsiasi attività a esecuzione prolungata. Il metodo restituisce un valore `StartCommandResult` che indica come o se il sistema deve gestire il riavvio del servizio dopo un arresto a causa di memoria insufficiente. Questa chiamata si verifica nel thread principale. Questo metodo viene descritto in dettaglio di seguito.
- `OnDestroy` &ndash; questo metodo viene chiamato quando il servizio viene eliminato definitivamente. Viene usato per eseguire la pulizia finale richiesta.

Il metodo importante per un servizio avviato è il metodo `OnStartCommand`. Verrà richiamato ogni volta che il servizio riceve una richiesta per eseguire alcune operazioni. Il frammento di codice seguente è un esempio di `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

Il primo parametro è un oggetto `Intent` contenente i metadati relativi al lavoro da eseguire. Il secondo parametro contiene un valore `StartCommandFlags` che fornisce alcune informazioni sulla chiamata al metodo. Questo parametro ha uno dei due valori possibili:

- `StartCommandFlag.Redelivery` &ndash; questo significa che il `Intent` è un nuovo recapito di un `Intent` precedente. Questo valore viene fornito quando il servizio ha restituito `StartCommandResult.RedeliverIntent` ma è stato interrotto prima di essere arrestato correttamente.
- `StartCommandFlag.Retry` &dash; questo valore viene ricevuto quando una chiamata `OnStartCommand` precedente non è riuscita e Android sta provando ad avviare di nuovo il servizio con la stessa finalità del precedente tentativo non riuscito.

Infine, il terzo parametro è un valore intero univoco per l'applicazione che identifica la richiesta. È possibile che più chiamanti possano richiamare lo stesso oggetto servizio. Questo valore viene utilizzato per associare una richiesta di arresto di un servizio con una determinata richiesta di avvio di un servizio. Questo argomento verrà discusso più dettagliatamente nella sezione [arresto del servizio](#Stopping_the_Service). 

Il valore `StartCommandResult` viene restituito dal servizio come suggerimento per Android sulle operazioni da eseguire se il servizio viene terminato a causa di vincoli di risorse. Esistono tre possibili valori per `StartCommandResult`:

- **[StartCommandResult. NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash; questo valore indica a Android che non è necessario riavviare il servizio che ha terminato. Come esempio, si consideri un servizio che genera anteprime per una raccolta in un'app. Se il servizio viene terminato, non è fondamentale ricreare immediatamente l'anteprima &ndash; l'anteprima può essere ricreata alla successiva esecuzione dell'app.
- **[StartCommandResult. Sticky](xref:Android.App.StartCommandResult.Sticky)** &ndash; indica a Android di riavviare il servizio, ma non di fornire l'ultimo intento che ha avviato il servizio. Se non sono presenti Intent in sospeso da gestire, viene fornita una `null` per il parametro Intent. Un esempio potrebbe essere un'app Music Player; il servizio verrà riavviato pronto per riprodurre musica, ma l'ultimo brano verrà riprodotto.
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; questo valore indica a Android di riavviare il servizio e recapitare l'ultimo `Intent`. Un esempio è un servizio che Scarica un file di dati per un'app. Se il servizio viene interrotto, è comunque necessario scaricare il file di dati. Restituendo `StartCommandResult.RedeliverIntent`, quando Android riavvia il servizio, verrà fornito anche l'intento (che include l'URL del file da scaricare) al servizio. In questo modo il download verrà riavviato o ripreso (a seconda dell'implementazione esatta del codice).

È disponibile un quarto valore per `StartCommandResult` `StartCommandResult.ContinuationMask` &ndash;. Questo valore viene restituito da `OnStartCommand` e descrive in che modo Android continuerà il servizio che ha terminato. Questo valore non viene in genere utilizzato per avviare un servizio.

In questo diagramma vengono illustrati gli eventi del ciclo di vita delle chiavi di un servizio avviato: 

![Diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita](started-services-images/started-service-01.png "Diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita.")

<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Arresto del servizio

Un servizio avviato continuerà a funzionare per un periodo illimitato; Android manterrà il servizio in esecuzione fino a quando sono disponibili risorse di sistema sufficienti. Il client deve arrestare il servizio oppure il servizio potrebbe arrestarsi automaticamente al termine del lavoro. Esistono due modi per arrestare un servizio: 

- **[Android. Content. Context. StopService ()](xref:Android.Content.Context.StopService*)** &ndash; un client, ad esempio un'attività, può richiedere l'arresto di un servizio chiamando il metodo `StopService`:

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[Android. app. Service. StopSelf ()](xref:Android.App.Service.StopSelf*)** &ndash; un servizio può arrestarsi in modo automatico richiamando il `StopSelf`:

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>Uso di startId per arrestare un servizio

Più chiamanti possono richiedere l'avvio di un servizio. Se è presente una richiesta di avvio in sospeso, il servizio può utilizzare il `startId` passato `OnStartCommand` per impedire che il servizio venga arrestato in modo anomalo. Il `startId` corrisponderà alla chiamata più recente a `StartService` e verrà incrementato ogni volta che viene chiamato. Se pertanto una richiesta successiva a `StartService` non ha ancora generato una chiamata a `OnStartCommand`, il servizio può chiamare `StopSelfResult`, passandogli il valore più recente di `startId` ricevuto (anziché semplicemente chiamando `StopSelf`). Se una chiamata a `StartService` non ha ancora generato una chiamata corrispondente a `OnStartCommand`, il sistema non arresterà il servizio, perché il `startId` usato nella chiamata `StopSelf` non corrisponderà alla chiamata di `StartService` più recente.

## <a name="related-links"></a>Collegamenti correlati

- [StartedServicesDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android. app. Service](xref:Android.App.Service)
- [Android. app. StartCommandFlags](xref:Android.App.StartCommandFlags)
- [Android. app. StartCommandResult](xref:Android.App.StartCommandResult)
- [Android. Content. BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android. Content. Intent](xref:Android.Content.Intent)
- [Android. OS. Handler](xref:Android.OS.Handler)
- [Android. widget. toast](xref:Android.Widget.Toast)
- [Icone della barra di stato](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
