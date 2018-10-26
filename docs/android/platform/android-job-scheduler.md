---
title: Utilità di pianificazione di processo Android
description: Questa guida illustra come pianificare l'attività in background usando l'API dell'utilità di pianificazione di processo Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 4bbb217fa8a3192905d016763b961e182224aa67
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108769"
---
# <a name="android-job-scheduler"></a>Utilità di pianificazione di processo Android

_Questa guida illustra come pianificare l'attività in background usando l'API di utilità di pianificazione processo Android, che è disponibile nei dispositivi Android che eseguono Android 5.0 (livello API 21) e versioni successive._


## <a name="overview"></a>Panoramica 

Uno dei modi migliori per mantenere reattiva verso utente un'applicazione Android è garantire che il lavoro lunga o complesso viene eseguito in background. Tuttavia, è importante che attività in background non influiranno negativamente sull'esperienza dell'utente con il dispositivo. 

Ad esempio, un processo in background potrebbe eseguire il polling di un sito Web ogni tre o quattro minuti per eseguire query per le modifiche a un determinato set di dati. Ciò sembra non dannoso, ma avrebbe un impatto disastroso sulla durata della batteria. L'applicazione verrà ripetutamente riattivare il dispositivo, elevare la CPU a uno stato di alimentazione superiore, accendere la radio, apportare le richieste di rete e quindi elaborando i risultati. Ottiene il peggiore perché il dispositivo verrà immediatamente non spegnere e tornare allo stato inattivo basso consumo. Attività in background in modo inadeguato pianificata potrebbe inavvertitamente tenere il dispositivo in stato di alimentazione superflua ed eccessiva. Questa attività apparentemente innocua (polling di un sito Web) sarà inutilizzabile il dispositivo in un periodo di tempo relativamente breve.

Android fornisce le API seguenti per facilitare l'esecuzione di lavoro in background, ma da soli non sono sufficienti per la pianificazione dei processi intelligenti. 

* **[Servizi Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; con finalità di servizi sono ideali per l'esecuzione del lavoro, tuttavia non forniscono alcuna possibilità di pianificare il lavoro.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; queste API consentono solo lavoro pianificata, ma non consentono di eseguire effettivamente il lavoro. Inoltre, il AlarmManager consente solo vincoli basato sul tempo, ovvero a generare un avviso a una determinata ora o dopo che è trascorso un determinato periodo di tempo. 
* **[Ricevitori di trasmissione](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; An Android app è possibile configurare ricevitori di trasmissioni per eseguire le operazioni in risposta a eventi a livello di sistema o Intent. Ricevitori di trasmissioni non forniscono tuttavia controllare quando il processo deve essere eseguito. Limitano inoltre le modifiche nel sistema operativo Android quando ricevitori di trasmissioni funzioneranno o i tipi di lavoro che rispondano alle. 

Esistono due funzionalità fondamentali per l'esecuzione in modo efficiente attività in background (talvolta detta un _processo in background_ o una _processo_):

1. **Le operazioni di pianificazione in modo intelligente** &ndash; è importante che quando un'applicazione esegue operazioni in background che esegue l'operazione come un elemento positivo. In teoria, l'applicazione non deve richiedere che un processo eseguito. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatti per quando il processo può eseguire e pianificare il processo con il sistema operativo che eseguirà il lavoro quando vengono soddisfatte le condizioni. In questo modo eseguire il processo per garantire la massima efficienza nel dispositivo Android. Ad esempio, possono essere eseguiti in batch le richieste di rete per l'esecuzione di tutti allo stesso tempo per l'utilizzo massimo di overhead coinvolti con la rete.
2. **Che incapsula le operazioni** &ndash; il codice per eseguire le attività in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sia relativamente facile da modificare la pianificazione se non viene completato il lavoro per qualche motivo.

Il pianificatore di processi Android è un framework incorporato per il sistema operativo Android che fornisce un'API fluent per semplificare la pianificazione del lavoro in background.  Il pianificatore di processi Android include i tipi seguenti:

* Il `Android.App.Job.JobScheduler` è un servizio di sistema che consente di pianificare, eseguire e annullare se necessario, i processi per conto di un'applicazione Android.
* Un `Android.App.Job.JobService` è una classe astratta che deve essere esteso con la logica che il processo verrà eseguito sul thread principale dell'applicazione. Ciò significa che il `JobService` riguarda il modo in cui il lavoro deve essere eseguita in modo asincrono.
* Un `Android.App.Job.JobInfo` oggetto contiene i criteri di supporto Android quando si desidera eseguire il processo.

Per pianificare il lavoro con l'utilità di pianificazione di processo Android, un'applicazione xamarin. Android è necessario incapsulare il codice in una classe che estende il `JobService` classe. `JobService` dispone di tre metodi del ciclo di vita che può essere chiamato nel corso della durata del processo:

* **bool (parametri JobParameters) OnStartJob** &ndash; questo metodo viene chiamato dal `JobScheduler` per eseguire il lavoro e in esecuzione sul thread principale dell'applicazione. È responsabilità del `JobService` per eseguire in modo asincrono il lavoro e `true` se è presente lavoro rimanente, o `false` se il lavoro viene eseguito.
    
    Quando il `JobScheduler` chiama questo metodo, verrà richiesta e mantenere un wakelock da Android per la durata del processo. Al termine del processo, è responsabilità del `JobService` per indicare il `JobScheduler` questo fatto da chiamata il `JobFinished` metodo (descritto di seguito).

* **JobFinished (parametri JobParameters, bool needsReschedule)** &ndash; deve essere chiamato dalle `JobService` per indicare il `JobScheduler` che il lavoro viene eseguito. Se `JobFinished` non viene chiamato, il `JobScheduler` non rimuoverà il wakelock, causando della batteria non necessari. 

* **bool (parametri JobParameters) OnStopJob** &ndash; viene chiamato quando il processo viene arrestato in modo anomalo da Android. Il metodo deve restituire `true` se il processo dovrebbe essere riprogrammato sulla base dei criteri di ripetizione dei tentativi (vedere di seguito in modo più dettagliato).

È possibile specificare _vincoli_ oppure _trigger_ che sarà assegnata alla rettifica quando un processo può o deve essere eseguito. Ad esempio, è possibile vincolare un processo in modo che venga eseguita solo quando si carica il dispositivo o per avviare un processo quando un'immagine viene eseguita.

Questa guida illustra in dettaglio come implementare un `JobService` classe e pianificare con il `JobScheduler`.

## <a name="requirements"></a>Requisiti

Il pianificatore di processi Android richiede il livello API Android (Android 5.0) 21 o versione successiva. 

## <a name="using-the-android-job-scheduler"></a>Tramite l'utilità di pianificazione di processo Android

Esistono tre passaggi per l'uso di API JobScheduler Android:

1. Implementare un tipo JobService per incapsulare il lavoro.
2. Usare un `JobInfo.Builder` oggetto per creare le `JobInfo` che conterrà i criteri per il `JobScheduler` per eseguire il processo. 
3. Pianificare il processo usando `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementare un JobService

Tutte le operazioni eseguite dalla libreria Android pianificatore di processi devono essere eseguita in un tipo che estende il `Android.App.Job.JobService` classe astratta. Creazione di un `JobService` è molto simile alla creazione di un `Service` con framework Android: 

1. Estendere il `JobService` classe.
2. Decorare la sottoclasse con la `ServiceAttribute` e impostare il `Name` parametro a una stringa che è costituita da nome del pacchetto e il nome della classe (vedere l'esempio seguente).
3. Impostare il `Permission` proprietà di `ServiceAttribute` alla stringa di `android.permission.BIND_JOB_SERVICE`.
4. Eseguire l'override di `OnStartJob` metodo, aggiungere il codice per svolgere il lavoro. Android verrà richiamato questo metodo sul thread principale dell'applicazione per eseguire il processo. Lavoro che richiederà più tempo che pochi millisecondi devono essere eseguite su un thread per evitare il blocco dell'applicazione.
5. Quando il lavoro viene completato, il `JobService` necessario chiamare il `JobFinished` (metodo). Questo metodo è la modalità `JobService` indica il `JobScheduler` che viene effettuato per lavoro. Errore durante la chiamata `JobFinished` comporterà il `JobService` inserire le richieste non necessarie nel dispositivo, ad abbreviare la durata della batteria. 
6. È consigliabile anche eseguire l'override di `OnStopJob` (metodo). Questo metodo viene chiamato da Android quando il processo viene arrestato prima che venga completato e fornisce il `JobService` con possibilità di eliminare correttamente eventuali risorse. Questo metodo deve restituire `true` se è necessario modificare la pianificazione del processo, o `false` se non è ricercata per rieseguire il processo.
   

Il codice seguente è riportato un esempio delle più semplici `JobService` per un'applicazione, usando la libreria TPL per eseguire in modo asincrono alcune operazioni:

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Creazione di un JobInfo per pianificare un processo

Le applicazioni xamarin. Android non creare un'istanza di un `JobService` , invece verranno passati direttamente un `JobInfo` dell'oggetto per il `JobScheduler`. Il `JobScheduler` verrà creata un'istanza di richiesta `JobService` oggetto, pianificazione e l'esecuzione di `JobService` in base ai metadati nel `JobInfo`. Oggetto `JobInfo` oggetto deve contenere le informazioni seguenti:

* **JobId** &ndash; si tratta di un' `int` valore utilizzato per identificare un processo per il `JobScheduler`. Riutilizzo di questo valore verrà aggiornati tutti i processi esistenti. Il valore deve essere univoco per l'applicazione. 
* **JobService** &ndash; questo parametro è un `ComponentName` che identifica in modo esplicito il tipo che il `JobScheduler` deve usare per eseguire un processo. 
  

Questo metodo di estensione viene illustrato come creare un `JobInfo.Builder` con un Android `Context`, ad esempio un'attività:

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob andsets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creats a JobInfo object.
```

Una potente funzionalità dell'utilità di pianificazione di processo Android è la possibilità di controllare quando viene eseguito un processo o in quali condizioni di un processo possono essere eseguite. Nella tabella seguente vengono descritti alcuni dei metodi su `JobInfo.Builder` che consentire a un'app influenzare quando eseguire un processo:  


|  Metodo | Descrizione   |
|---|---|
| `SetMinimumLatency`  | Specifica che un ritardo, in millisecondi, che dovrebbe essere esaminato prima di un processo venga eseguito. |
| `SetOverridingDeadline`  | Dichiara che il processo è necessario eseguire prima che sia trascorso questo tempo (in millisecondi). |
| `SetRequiredNetworkType`  | Specifica i requisiti di rete per un processo. |
| `SetRequiresBatteryNotLow` | Il processo può essere eseguita solo quando il dispositivo non viene visualizzato un avviso "batteria in esaurimento" all'utente. |
| `SetRequiresCharging` | Il processo può essere eseguita solo quando la batteria in carica. |
| `SetDeviceIdle` | Il processo verrà eseguito quando il dispositivo è occupato. |
| `SetPeriodic` | Specifica che il processo deve essere eseguito regolarmente. |
| `SetPersisted` | Il processo deve perisist dopo i riavvii di dispositivo. | 


Il `SetBackoffCriteria` offre alcune indicazioni su come tempo il `JobScheduler` deve attendere prima di provare a rieseguire un processo. Esistono due parti per i criteri di backoff: un ritardo in millisecondi (valore predefinito di 30 secondi) e il tipo di sicurezza che deve essere utilizzato (talvolta come il _criterio di backoff_ o il _criterio di ripetizione_) . I due criteri incapsulati nel `Android.App.Job.BackoffPolicy` enum:

* `BackoffPolicy.Exponential` &ndash; Un criterio di backoff esponenziale aumenterà in modo esponenziale il valore di backoff iniziale dopo ogni errore. La prima volta che un processo non riesce, la libreria attenderà l'intervallo iniziale specificato prima del rinvio del processo: esempio 30 secondi. La seconda volta che il processo non riesce, la libreria attenderà almeno 60 secondi prima di provare a eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria di 120 secondi di attesa e così via. Rappresenta il valore predefinito.
* `BackoffPolicy.Linear` &ndash; Questa strategia è un backoff lineare che il processo dovrebbe essere riprogrammato per eseguire a intervalli prestabiliti (fino a quando non ha esito positivo). Backoff lineare è particolarmente adatto per l'elaborazione che deve essere completate prima possibile o per i problemi che consentirà di risolvere rapidamente se stessi. 

Per altre informazioni su creazione una `JobInfo` oggetti, Leggi [documentazione di Google per il `JobInfo.Builder` classe](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passaggio di parametri a un processo tramite il JobInfo

I parametri vengono passati a un processo tramite la creazione di un `PersistableBundle` che viene passato con la `Job.Builder.SetExtras` metodo:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

Il `PersistableBundle` avviene dal `Android.App.Job.JobParameters.Extras` proprietà nel `OnStartJob` metodo di un `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Per pianificare un processo, otterrà un riferimento a un'applicazione xamarin. Android le `JobScheduler` servizio di sistema e chiamare il `JobScheduler.Schedule` metodo con il `JobInfo` oggetto creato nel passaggio precedente. `JobScheduler.Schedule` verrà restituito immediatamente con uno dei due valori interi:

* **JobScheduler.ResultSuccess** &ndash; il processo è stato pianificato. 
* **JobScheduler.ResultFailure** &ndash; il processo non è stato pianificato. Ciò in genere è dovuto in conflitto `JobInfo` parametri.

Questo codice è un esempio di pianificazione di un processo e avvisare l'utente dei risultati del tentativo di pianificazione:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>Annullare un processo

È possibile annullare tutti i processi che sono stati pianificati, o semplicemente un singolo processo con il `JobsScheduler.CancelAll()` metodo o il `JobScheduler.Cancel(jobId)` metodo:

```csharp
// Cancel all jobs
jobSchduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come usare l'utilità di pianificazione di processi Android per operare in modo intelligente in background. Illustrata la procedura per incapsulare il lavoro deve essere eseguita come un `JobService` e su come usare il `JobScheduler` pianificare il lavoro, specificare i criteri con un `JobTrigger` e modalità di gestione degli errori con un `RetryStrategy`.

## <a name="related-links"></a>Collegamenti correlati

- [Intelligent pianificazione dei processi](https://developer.android.com/topic/performance/scheduling.html)
- [Riferimento all'API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Pianificazione dei processi, ad esempio un vero professionista grazie JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android della batteria e ottimizzazioni della memoria - 2016 dei / o Google (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Ruppert - Xamarin University](https://www.youtube.com/watch?v=aSjBBPYjelE)