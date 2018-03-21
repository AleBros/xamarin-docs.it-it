---
title: "Utilità di pianificazione di processo Android"
description: "Questa guida viene descritto come pianificare il lavoro in background mediante l'API dell'utilità di pianificazione processo Android."
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: dc72b7e4da330185b00541f923d9c4b64b91bc95
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2018
---
# <a name="android-job-scheduler"></a>Utilità di pianificazione di processo Android

_Questa guida viene descritto come pianificare il lavoro in background mediante l'API dell'utilità di pianificazione processo Android, disponibile nei dispositivi Android che eseguono Android 5.0 (livello API 21) e versioni successive._


## <a name="overview"></a>Panoramica 

Uno dei modi migliori per mantenere la reattività all'utente di un'applicazione Android è per assicurarsi che il lavoro complesso o a esecuzione prolungata viene eseguito in background. Tuttavia, è importante che operazioni in background non influiranno negativamente sull'esperienza dell'utente con il dispositivo. 

Ad esempio, un processo in background potrebbe eseguire il polling di un sito Web tre o quattro minuti per eseguire query per le modifiche a un determinato set di dati. Può sembrare un passo grave, ma avrebbe un impatto disastroso sulla durata della batteria. L'applicazione verrà ripetutamente riattivare il dispositivo, elevare la CPU a uno stato di alimentazione superiore, accendere la radio, le richieste di rete e quindi elaborare i risultati. Ottiene il peggiore perché il dispositivo verrà immediatamente non spegnere e ripristinare lo stato di inattività di basso consumo. Operazioni in background pianificata in modo inadeguato potrebbero inavvertitamente tenere il dispositivo in stato di alimentazione superflua ed eccessiva. Questa attività apparentemente innocuo (polling di un sito Web) verrà eseguito il rendering del dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le API seguenti per facilitare l'esecuzione di lavoro in background ma da soli non sono sufficienti per la pianificazione dei processi intelligenti. 

* **[Servizi preventivi](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; con finalità di servizi sono ideali per l'esecuzione del lavoro, ma non consentono di pianificare il lavoro.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; queste API consentono solo di lavoro essere pianificato ma non si specifica alcuna possibilità di eseguire effettivamente il lavoro. Inoltre, il AlarmManager consente solo limiti di tempo in base che si intende generare un avviso a una determinata ora o una volta trascorso un determinato periodo di tempo. 
* **[Trasmissione ricevitori](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; app di Android è possibile configurare broadcast ricevitori per eseguire il lavoro in risposta a eventi a livello di sistema o i tipi. Tuttavia, i ricevitori di broadcast non forniscono qualsiasi controllo dell'esecuzione del processo deve essere. Limitano inoltre le modifiche nel sistema operativo Android quando è funzionerà ricevitori broadcast o i tipi di lavoro in grado di rispondere a. 

Esistono due funzionalità fondamentali per in modo efficiente l'esecuzione dell'attività in background (talvolta detto una _processo in background_ o una _processo_):

1. **In modo intelligente pianificazione del lavoro** &ndash; è importante che quando un'applicazione esegue operazioni in background che esegue l'operazione come buona cittadinanza. Idealmente, l'applicazione non deve richiedere che un processo eseguito. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per quando il processo può eseguire e quindi pianificare il processo con il sistema operativo che verrà eseguito il lavoro quando vengono soddisfatte le condizioni. In questo modo eseguire il processo per garantire la massima efficienza nel dispositivo Android. Ad esempio, possono essere eseguiti in batch le richieste di rete per l'esecuzione di tutti allo stesso tempo per l'utilizzo massimo di overhead coinvolti con la rete.
2. **Che incapsula il lavoro** &ndash; il codice per eseguire le operazioni in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e che sia relativamente facile da modificare la pianificazione se non viene eseguito il lavoro per qualche motivo.

Il pianificatore di processi Android è un framework incorporato per il sistema operativo Android che fornisce un'API intuitiva per semplificare la pianificazione del lavoro in background.  Il pianificatore di processi Android include i seguenti tipi:

* Il `Android.App.Job.JobScheduler` è un servizio di sistema che consente di pianificare, eseguire e annullare se necessario, i processi per conto di un'applicazione Android.
* Un `Android.App.Job.JobService` è una classe astratta che deve essere estesa con la logica che verrà eseguito il processo nel thread principale dell'applicazione. Ciò significa che il `JobService` è responsabile per la modalità di lavoro da eseguire in modo asincrono.
* Un `Android.App.Job.JobInfo` oggetto contiene i criteri da utilizzare per assumere Android quando il processo da eseguire.

Per pianificare il lavoro con il pianificatore di processi Android, un'applicazione di xamarin. Android deve incapsulare il codice in una classe che estende la `JobService` classe. `JobService` dispone di tre metodi del ciclo di vita che può essere chiamato nel corso della durata del processo:

* **bool (parametri JobParameters) OnStartJob** &ndash; questo metodo viene chiamato dal `JobScheduler` per eseguire il lavoro e viene eseguito nel thread principale dell'applicazione. È responsabilità del `JobService` per eseguire in modo asincrono il lavoro e `true` se è presente lavoro rimanente, o `false` se l'operazione viene eseguita.
    
    Quando il `JobScheduler` chiama questo metodo, verrà richiesta e mantenere un wakelock da Android per la durata del processo. Al termine del processo, è responsabilità del `JobService` per indicare il `JobScheduler` questo fatto dalla chiamata di `JobFinished` metodo (descritto di seguito).

* **JobFinished (parametri JobParameters, needsReschedule bool)** &ndash; questo metodo deve essere chiamato dal `JobService` per indicare il `JobScheduler` che viene completata. Se `JobFinished` non viene chiamato, il `JobScheduler` non rimuoverà wakelock, causando batteria non necessari. 

* **bool (parametri JobParameters) OnStopJob** &ndash; viene chiamato quando il processo viene interrotto in modo anomalo da Android. Deve restituire `true` se il processo deve essere riprogrammato in base ai criteri di ripetizione (illustrati di seguito in modo più dettagliato).

È possibile specificare _vincoli_ oppure _trigger_ che controlleranno quando un processo può o deve essere eseguito. Ad esempio, è possibile vincolare un processo in modo che venga eseguita solo quando si carica il dispositivo o per avviare un processo di quando un'immagine viene eseguita.

Questa Guida verrà illustrato in dettaglio come implementare un `JobService` classe e la pianificazione con il `JobScheduler`.

## <a name="requirements"></a>Requisiti

Il pianificatore di processi Android richiede l'API Android livello 21 (Android 5.0) o versione successiva. 

## <a name="using-the-android-job-scheduler"></a>Tramite l'utilità di pianificazione di processo Android

Esistono tre passaggi per l'utilizzo di API JobScheduler Android:

1. Implementare un tipo JobService per incapsulare il lavoro.
2. Utilizzare un `JobInfo.Builder` oggetto per creare le `JobInfo` che conterrà i criteri per il `JobScheduler` per eseguire il processo. 
3. Pianificare il processo tramite `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementare un JobService

Tutte le operazioni eseguite dalla libreria Android pianificatore di processi devono essere eseguita in un tipo che estende la `Android.App.Job.JobService` classe astratta. Creazione di un `JobService` è molto simile alla creazione di un `Service` con Android framework: 

1. Estendere la `JobService` classe.
2. Decorare la sottoclasse con la `ServiceAttribute` e impostare il `Name` parametro in una stringa che è costituita da nome del pacchetto e il nome della classe (vedere l'esempio seguente).
3. Impostare il `Permission` proprietà di `ServiceAttribute` alla stringa di `android.permission.BIND_JOB_SERVICE`.
4. Eseguire l'override di `OnStartJob` metodo, aggiungendo il codice per svolgere il lavoro. Android verrà richiamato questo metodo sul thread principale dell'applicazione per eseguire il processo. Lavoro che richiederà più tempo che devono essere eseguite pochi millisecondi in un thread per evitare il blocco dell'applicazione.
5. Quando viene completata, il `JobService` necessario chiamare il `JobFinished` metodo. Questo metodo è la modalità `JobService` indica il `JobScheduler` di lavoro. Un errore nella chiamata `JobFinished` comporterà il `JobService` inserire le richieste non necessarie nel dispositivo, ad abbreviare la durata della batteria. 
6. È consigliabile inoltre eseguire l'override di `OnStopJob` metodo. Questo metodo viene chiamato da Android quando il processo viene arrestato prima che venga terminato e fornisce il `JobService` opportunità di eliminare correttamente le risorse. Questo metodo deve restituire `true` se è necessario ripianificare il processo o `false` se non è desireable rieseguire il processo.
   

Il codice riportato di seguito è riportato un esempio delle più semplici `JobService` per un'applicazione, utilizzando la libreria TPL per eseguire in modo asincrono alcune operazioni:

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

Le applicazioni di xamarin. Android non creare un'istanza di un `JobService` direttamente, in alternativa, si passerà una `JobInfo` dell'oggetto per il `JobScheduler`. Il `JobScheduler` crea un'istanza di richiesta `JobService` oggetto, pianificazione e l'esecuzione di `JobService` in base ai metadati nel `JobInfo`. Oggetto `JobInfo` oggetto deve contenere le informazioni seguenti:

* **JobId** &ndash; si tratta di un' `int` valore utilizzato per identificare un processo per il `JobScheduler`. Riutilizzo di questo valore verrà aggiornato tutti i processi esistenti. Il valore deve essere univoco per l'applicazione. 
* **JobService** &ndash; questo parametro è un `ComponentName` che identifica in modo esplicito il tipo che il `JobScheduler` deve utilizzare per eseguire un processo. 
  

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

Una funzionalità molto efficiente dell'utilità di pianificazione processo Android è la possibilità di controllare quando viene eseguito un processo o in quali condizioni di un processo possono essere eseguite. Nella tabella seguente vengono descritti alcuni dei metodi in `JobInfo.Builder` che consentono un'app influenzare quando un processo può eseguire:  


|  Metodo | Descrizione   |
|---|---|
| `SetMinimumLatency`  | Specifica che un ritardo (in millisecondi) che debba essere osservato prima di un processo venga eseguito. |
| `SetOverridingDeadline`  | Dichiara che il processo deve essere eseguito prima che sia trascorso questo tempo (in millisecondi). |
| `SetRequiredNetworkType`  | Specifica i requisiti di rete per un processo. |
| `SetRequiresBatteryNotLow` | Il processo venga eseguito solo quando il dispositivo non viene visualizzato un avviso "batteria in esaurimento" all'utente. |
| `SetRequiresCharging` | Il processo venga eseguito solo quando la batteria in carica. |
| `SetDeviceIdle` | Il processo verrà eseguito quando il dispositivo è occupato. |
| `SetPeriodic` | Specifica che il processo deve essere eseguito regolarmente. |
| `SetPersisted` | Il processo deve essere perisist tra i riavvii di dispositivo. | 


Il `SetBackoffCriteria` fornite alcune indicazioni su come tempo `JobScheduler` deve attendere prima di provare a eseguire di nuovo un processo. Esistono due parti per i criteri di backoff: un ritardo in millisecondi (valore predefinito di 30 secondi) e il tipo di Backoff che deve essere utilizzato (talvolta detto il _criteri di backoff_ o il _criteri di ripetizione_) . I due criteri sono incapsulati nel `Android.App.Job.BackoffPolicy` enum:

* `BackoffPolicy.Exponential` &ndash; Criteri di backoff esponenziale aumenterà in modo esponenziale il valore di backoff iniziale dopo ogni errore. La prima volta che un processo non riesce, la libreria attenderà l'intervallo iniziale specificato prima di ripianificazione del processo-esempio 30 secondi. La seconda volta che il processo non riesce, la libreria dovrà attendere almeno 60 secondi prima di tentare di eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria di 120 secondi di attesa e così via. Rappresenta il valore predefinito.
* `BackoffPolicy.Linear` &ndash; Questa strategia è un backoff lineare che dovrebbe essere riprogrammato il processo da eseguire a intervalli predefiniti (fino a quando non ha esito positivo). Backoff lineare è ideale per l'elaborazione che deve essere completate presto o per i problemi verranno risolti rapidamente. 

Per creare ulteriori dettagli su un `JobInfo` oggetto, leggere [documentazione di Google per il `JobInfo.Builder` classe](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passaggio di parametri a un processo tramite il JobInfo

I parametri vengono passati a un processo tramite la creazione di un `PersistableBundle` passato con la `Job.Builder.SetExtras` metodo:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

Il `PersistableBundle` avviene dal `Android.App.Job.JobParameters.Extras` proprietà il `OnStartJob` metodo di un `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Per pianificare un processo, otterrà un riferimento a un'applicazione di xamarin. Android il `JobScheduler` servizio di sistema e chiamare il `JobScheduler.Schedule` metodo con il `JobInfo` oggetto creato nel passaggio precedente. `JobScheduler.Schedule` verrà restituito immediatamente con uno dei due valori interi:

* **JobScheduler.ResultSuccess** &ndash; il processo è stato pianificato. 
* **JobScheduler.ResultFailure** &ndash; non è stato possibile pianificare il processo. Questo è in genere dovuto in conflitto `JobInfo` parametri.

Questo codice è riportato un esempio di pianificazione di un processo e notifica l'utente dei risultati del tentativo di programmazione:

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

È possibile annullare tutti i processi che sono stati pianificati, o semplicemente un singolo processo tramite il `JobsScheduler.CancelAll()` metodo o `JobScheduler.Cancel(jobId)` metodo:

```csharp
// Cancel all jobs
jobSchduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Riepilogo

Questa guida viene spiegato come utilizzare il pianificatore di processi Android per lavorare in modo intelligente in background. Illustrata la procedura per incapsulare il lavoro deve essere eseguita come un `JobService` e come utilizzare la `JobScheduler` pianificare il lavoro, specificare i criteri con un `JobTrigger` e modalità di gestione di errori con un `RetryStrategy`.

## <a name="related-links"></a>Collegamenti correlati

- [Intelligente pianificazione dei processi](https://developer.android.com/topic/performance/scheduling.html)
- [Riferimento all'API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Pianificazione dei processi avanzati con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android batteria e ottimizzazioni memoria - 2016 dei / o Google (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [University Xamarin Android JobScheduler - René Ruppert-](https://www.youtube.com/watch?v=aSjBBPYjelE)