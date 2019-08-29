---
title: Utilità di pianificazione di processo Android
description: Questa guida illustra come pianificare il lavoro in background usando l'API dell'utilità di pianificazione dei processi Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 95d4194e0ed1a1da435a233e40a74f506c49b539
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119870"
---
# <a name="android-job-scheduler"></a>Utilità di pianificazione di processo Android

_Questa guida illustra come pianificare il lavoro in background usando l'API dell'utilità di pianificazione dei processi Android, disponibile nei dispositivi Android che eseguono Android 5,0 (livello API 21) e versioni successive._


## <a name="overview"></a>Panoramica 

Uno dei modi migliori per garantire che un'applicazione Android sia in grado di rispondere all'utente è garantire che il lavoro complesso o a esecuzione prolungata venga eseguito in background. Tuttavia, è importante che il lavoro in background non influisca negativamente sull'esperienza dell'utente con il dispositivo. 

Un processo in background, ad esempio, può eseguire il polling di un sito Web ogni tre o quattro minuti per cercare le modifiche apportate a un determinato set di dati Questa situazione sembra benigna, ma potrebbe avere un effetto disastroso sulla durata della batteria. L'applicazione riattiverà ripetutamente il dispositivo, eleva la CPU a uno stato di alimentazione superiore, accende le radio, effettua le richieste di rete e quindi elabora i risultati. Peggiora perché il dispositivo non si spegne immediatamente e torna allo stato di inattività a basso consumo. Il lavoro in background pianificato in modo non corretto può inavvertitamente impedire che il dispositivo si trovi in uno stato con requisiti di alimentazione superflui e Questa attività apparentemente innocente (polling di un sito Web) rende il dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le API seguenti che consentono di eseguire il lavoro in background, ma non sono sufficienti per la pianificazione intelligente dei processi. 

- **[Servizi Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; I servizi per finalità sono ottimi per l'esecuzione del lavoro, ma non forniscono alcun modo per pianificare il lavoro.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Queste API consentono solo la pianificazione del lavoro, ma non forniscono alcun modo per eseguire effettivamente il lavoro. Inoltre, AlarmManager consente solo vincoli basati sul tempo, il che significa generare un allarme in un determinato momento o dopo che è trascorso un determinato periodo di tempo. 
- **[Ricevitori broadcast](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Un'app Android può configurare i ricevitori di trasmissioni per eseguire il lavoro in risposta a eventi a livello di sistema o Intent. Tuttavia, i ricevitori di trasmissioni non forniscono alcun controllo sul momento in cui il processo deve essere eseguito. Inoltre, le modifiche apportate al sistema operativo Android verranno limitate quando i ricevitori di trasmissioni funzioneranno o i tipi di lavoro a cui possono rispondere. 

Sono disponibili due funzionalità principali per eseguire in modo efficiente il lavoro in background (talvolta definito _processo in background_ o _processo_):

1. **Pianificazione intelligente del lavoro** &ndash; È importante che, quando un'applicazione esegue il lavoro in background, in quanto è un valido cittadino. Idealmente, l'applicazione non deve richiedere l'esecuzione di un processo. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per il momento in cui il processo può essere eseguito, quindi pianificare il processo con il sistema operativo che eseguirà il lavoro quando vengono soddisfatte le condizioni. Ciò consente ad Android di eseguire il processo per garantire la massima efficienza del dispositivo. Ad esempio, è possibile che le richieste di rete vengano eseguite in batch in modo che vengano eseguite contemporaneamente per sfruttare al massimo l'overhead richiesto per la rete.
2. **Incapsulamento del lavoro** &ndash; Il codice per eseguire le operazioni in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sarà relativamente semplice da ripianificare se il lavoro non viene completato per qualche motivo.

L'utilità di pianificazione dei processi Android è un framework integrato nel sistema operativo Android che fornisce un'API Fluent per semplificare la pianificazione del lavoro in background.  L'utilità di pianificazione dei processi Android è costituita dai tipi seguenti:

- `Android.App.Job.JobScheduler` È un servizio di sistema usato per pianificare, eseguire e, se necessario, annullare i processi per conto di un'applicazione Android.
- Un `Android.App.Job.JobService` oggetto è una classe astratta che deve essere estesa con la logica che eseguirà il processo sul thread principale dell'applicazione. Ciò significa che `JobService` è responsabile della modalità di esecuzione del lavoro in modo asincrono.
- Un `Android.App.Job.JobInfo` oggetto include i criteri per guidare Android quando il processo deve essere eseguito.

Per pianificare il lavoro con l'utilità di pianificazione dei processi Android, un'applicazione Novell. Android deve incapsulare il codice in una `JobService` classe che estende la classe. `JobService`dispone di tre metodi del ciclo di vita che possono essere chiamati durante il processo:

- **bool OnStartJob (parametri JobParameters)** Questo metodo viene chiamato `JobScheduler` da per eseguire il lavoro e viene eseguito sul thread principale dell'applicazione. &ndash; È responsabilità di `JobService` eseguire in modo asincrono il lavoro e `true` se il lavoro rimane o `false` se il lavoro viene eseguito.
    
    `JobScheduler` Quando chiama questo metodo, richiede e mantiene un Wakelock da Android per la durata del processo. Al termine del processo, è responsabilità dell'oggetto `JobService` per `JobScheduler` indicare a di questo fatto chiamare il `JobFinished` metodo (descritto di seguito).

- **JobFinished (parametri JobParameters, bool needsReschedule)** Questo metodo deve essere chiamato `JobService` da per indicare al `JobScheduler` che il lavoro è stato eseguito. &ndash; Se `JobFinished` non viene chiamato `JobScheduler` , non rimuoverà il Wakelock, causando un esaurimento della batteria superfluo. 

- **bool OnStopJob (parametri JobParameters)** &ndash; Questo metodo viene chiamato quando il processo viene arrestato in modo anomalo da Android. Deve restituire `true` se il processo deve essere ripianificato in base ai criteri di ripetizione dei tentativi (descritti più avanti in modo più dettagliato).

È possibile specificare _vincoli_ o _trigger_ che controlleranno quando un processo può o deve essere eseguito. Ad esempio, è possibile vincolare un processo in modo che venga eseguito solo quando il dispositivo viene caricato o per avviare un processo quando viene acquisita un'immagine.

In questa guida verrà illustrato in dettaglio come implementare una `JobService` classe e pianificarla `JobScheduler`con.

## <a name="requirements"></a>Requisiti

L'utilità di pianificazione dei processi Android richiede il livello di API Android 21 (Android 5,0) o versione successiva. 

## <a name="using-the-android-job-scheduler"></a>Uso dell'utilità di pianificazione dei processi Android

Per l'uso dell'API JobScheduler Android sono disponibili tre passaggi:

1. Implementare un tipo JobService per incapsulare il lavoro.
2. Utilizzare un `JobInfo.Builder` oggetto per creare l' `JobInfo` oggetto che conterrà i criteri per l' `JobScheduler` esecuzione del processo. 
3. Pianificare il processo usando `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementare una JobService

Tutto il lavoro eseguito dalla libreria dell'utilità di pianificazione dei processi Android deve essere eseguito in un tipo `Android.App.Job.JobService` che estende la classe astratta. La creazione `JobService` di un oggetto è molto simile `Service` alla creazione di una con il Framework Android: 

1. Estendere la `JobService` classe.
2. Decorare la sottoclasse con `ServiceAttribute` e impostare il `Name` parametro su una stringa costituita dal nome del pacchetto e dal nome della classe (vedere l'esempio seguente).
3. Impostare la `Permission` proprietà sull'oggetto `ServiceAttribute` sulla stringa `android.permission.BIND_JOB_SERVICE`.
4. Eseguire l' `OnStartJob` override del metodo, aggiungendo il codice per eseguire il lavoro. Android richiamerà questo metodo sul thread principale dell'applicazione per eseguire il processo. Per evitare di bloccare l'applicazione, è necessario eseguire un lavoro che comporterà più tempo.
5. Al termine del lavoro, `JobService` deve chiamare il `JobFinished` metodo. Questo metodo indica il `JobService` modo in `JobScheduler` cui il lavoro viene eseguito. La mancata `JobFinished` chiamata comporta l' `JobService` inserimento di richieste inutili sul dispositivo, riducendo la durata della batteria. 
6. È consigliabile anche eseguire l'override del `OnStopJob` metodo. Questo metodo viene chiamato da Android quando il processo viene arrestato prima del completamento e fornisce all'oggetto la `JobService` possibilità di eliminare correttamente le risorse. Questo metodo deve restituire `true` se è necessario ripianificare il processo o `false` se non è auspicabile eseguire di nuovo il processo.
   

Il codice seguente è un esempio del più semplice `JobService` per un'applicazione, usando TPL per eseguire in modo asincrono alcune operazioni:

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

Le applicazioni Novell. Android non creano un' `JobService` istanza diretta di, bensì passano un `JobInfo` oggetto a `JobScheduler`. Creerà un'istanza dell'oggetto richiesto `JobService` , pianificando ed eseguendo il `JobService` secondo i metadati in `JobInfo`. `JobScheduler` Un `JobInfo` oggetto deve contenere le seguenti informazioni:

- **JobID** si tratta di `int` un valore utilizzato per `JobScheduler`identificare un processo in. &ndash; Il riutilizzo di questo valore aggiornerà eventuali processi esistenti. Il valore deve essere univoco per l'applicazione. 
- **JobService** Questo parametro è un `ComponentName` oggetto che `JobScheduler` identifica in modo esplicito il tipo che deve essere utilizzato da per eseguire un processo. &ndash; 
  

Questo metodo di estensione illustra come creare un `JobInfo.Builder` con Android `Context`, ad esempio un'attività:

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

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Una potente funzionalità dell'utilità di pianificazione dei processi Android è la possibilità di controllare quando viene eseguito un processo o in quali condizioni può essere eseguito un processo. Nella tabella seguente vengono descritti alcuni dei metodi `JobInfo.Builder` di che consentono a un'applicazione di influenzare quando è possibile eseguire un processo:  


|  Metodo | Descrizione   |
|---|---|
| `SetMinimumLatency`  | Specifica che deve essere osservato un ritardo (in millisecondi) prima dell'esecuzione di un processo. |
| `SetOverridingDeadline`  | Dichiara che il processo deve essere eseguito prima della scadenza (in millisecondi). |
| `SetRequiredNetworkType`  | Specifica i requisiti di rete per un processo. |
| `SetRequiresBatteryNotLow` | Il processo può essere eseguito solo quando l'utente non visualizza un avviso "batteria insufficiente". |
| `SetRequiresCharging` | Il processo può essere eseguito solo quando la batteria viene addebitata. |
| `SetDeviceIdle` | Il processo verrà eseguito quando il dispositivo è occupato. |
| `SetPeriodic` | Specifica che il processo deve essere eseguito regolarmente. |
| `SetPersisted` | Il processo deve essere perisist tra i riavvii del dispositivo. | 


Fornisce alcune indicazioni sulla durata dell' `JobScheduler` attesa prima di tentare di eseguire nuovamente un processo. `SetBackoffCriteria` Sono disponibili due parti per i criteri backoff: un ritardo in millisecondi (valore predefinito di 30 secondi) e il tipo di back off da usare (a volte definito _criterio backoff_ o i _criteri di ripetizione dei tentativi_). I due criteri sono incapsulati nell' `Android.App.Job.BackoffPolicy` enumerazione:

- `BackoffPolicy.Exponential`&ndash; Un criterio backoff esponenziale aumenterà in modo esponenziale il valore iniziale di backoff dopo ogni errore. La prima volta che si verifica un errore in un processo, la libreria attenderà l'intervallo iniziale specificato prima di ripianificare il processo, ad esempio 30 secondi. La seconda volta che il processo non riesce, la libreria attenderà almeno 60 secondi prima di provare a eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria attenderà 120 secondi e così via. Rappresenta il valore predefinito.
- `BackoffPolicy.Linear`&ndash; Questa strategia è un backoff lineare che il processo deve essere ripianificato per l'esecuzione a intervalli prestabiliti (fino a quando non riesce). Il backoff lineare è più adatto per il lavoro che deve essere completato il prima possibile o per i problemi che si risolveranno rapidamente. 

Per ulteriori informazioni sulla creazione di `JobInfo` un oggetto, vedere [la documentazione di Google relativa `JobInfo.Builder` alla classe](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passaggio di parametri a un processo tramite JobInfo

`PersistableBundle` I`Job.Builder.SetExtras` parametri vengono passati a un processo creando un oggetto che viene passato insieme al metodo:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

È `PersistableBundle` possibile accedere `Android.App.Job.JobParameters.Extras` alla proprietà nel `OnStartJob` metodo di un oggetto `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Per pianificare un processo, un'applicazione Novell. Android otterrà un riferimento al servizio di `JobScheduler` sistema e chiamerà il `JobScheduler.Schedule` metodo con l' `JobInfo` oggetto creato nel passaggio precedente. `JobScheduler.Schedule`restituirà immediatamente uno dei due valori integer:

- **JobScheduler. ResultSuccess** &ndash; il processo è stato pianificato correttamente. 
- **JobScheduler. ResultFailure** &ndash; non è stato possibile pianificare il processo. Questa situazione è in genere causata da `JobInfo` parametri in conflitto.

Questo codice è un esempio di pianificazione di un processo e di notificare all'utente i risultati del tentativo di pianificazione:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```
 
### <a name="cancelling-a-job"></a>Annullamento di un processo

È possibile annullare tutti i processi pianificati o solo un singolo processo usando il `JobsScheduler.CancelAll()` metodo o il `JobScheduler.Cancel(jobId)` metodo:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come usare l'utilità di pianificazione dei processi Android per eseguire in modo intelligente il lavoro in background. Viene illustrato come incapsulare il lavoro `JobService` da eseguire come e come `JobScheduler` utilizzare per pianificare il lavoro, specificando i criteri con un oggetto `JobTrigger` e la modalità di gestione degli errori con un oggetto `RetryStrategy`.

## <a name="related-links"></a>Collegamenti correlati

- [Pianificazione intelligente del processo](https://developer.android.com/topic/performance/scheduling.html)
- [Informazioni di riferimento sull'API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Pianificazione di processi come un Pro con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Ottimizzazioni della memoria e della batteria Android-Google I/O 2016 (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
