---
title: Utilità di pianificazione di processo Android
description: Questa guida illustra come pianificare il lavoro in background usando l'API dell'utilità di pianificazione dei processi Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 4d28b80b32ff0d20afbe643d9c000f301a8ea582
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027817"
---
# <a name="android-job-scheduler"></a>Utilità di pianificazione di processo Android

_Questa guida illustra come pianificare il lavoro in background usando l'API dell'utilità di pianificazione dei processi Android, disponibile nei dispositivi Android che eseguono Android 5,0 (livello API 21) e versioni successive._

## <a name="overview"></a>Panoramica 

Uno dei modi migliori per garantire che un'applicazione Android sia in grado di rispondere all'utente è garantire che il lavoro complesso o a esecuzione prolungata venga eseguito in background. Tuttavia, è importante che il lavoro in background non influisca negativamente sull'esperienza dell'utente con il dispositivo. 

Un processo in background, ad esempio, può eseguire il polling di un sito Web ogni tre o quattro minuti per cercare le modifiche apportate a un determinato set di dati Questa situazione sembra benigna, ma potrebbe avere un effetto disastroso sulla durata della batteria. L'applicazione riattiverà ripetutamente il dispositivo, eleva la CPU a uno stato di alimentazione superiore, accende le radio, effettua le richieste di rete e quindi elabora i risultati. Peggiora perché il dispositivo non si spegne immediatamente e torna allo stato di inattività a basso consumo. Il lavoro in background pianificato in modo non corretto può inavvertitamente impedire che il dispositivo si trovi in uno stato con requisiti di alimentazione superflui e Questa attività apparentemente innocente (polling di un sito Web) rende il dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le API seguenti che consentono di eseguire il lavoro in background, ma non sono sufficienti per la pianificazione intelligente dei processi. 

- I servizi **[Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; Intent sono ottimi per l'esecuzione del lavoro, ma non forniscono alcun modo per pianificare il lavoro.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; queste API consentono solo la pianificazione del lavoro, ma non forniscono alcun modo per eseguire effettivamente il lavoro. Inoltre, AlarmManager consente solo vincoli basati sul tempo, il che significa generare un allarme in un determinato momento o dopo che è trascorso un determinato periodo di tempo. 
- I **[ricevitori di trasmissioni](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; un'app Android possono configurare i ricevitori di trasmissioni per eseguire il lavoro in risposta a eventi a livello di sistema o Intent. Tuttavia, i ricevitori di trasmissioni non forniscono alcun controllo sul momento in cui il processo deve essere eseguito. Inoltre, le modifiche apportate al sistema operativo Android verranno limitate quando i ricevitori di trasmissioni funzioneranno o i tipi di lavoro a cui possono rispondere. 

Sono disponibili due funzionalità principali per eseguire in modo efficiente il lavoro in background (talvolta definito _processo in background_ o _processo_):

1. **Pianificare in modo intelligente il lavoro** &ndash; è importante che, quando un'applicazione esegue il lavoro in background, come un valido cittadino. Idealmente, l'applicazione non deve richiedere l'esecuzione di un processo. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per il momento in cui il processo può essere eseguito, quindi pianificare il processo con il sistema operativo che eseguirà il lavoro quando vengono soddisfatte le condizioni. Ciò consente ad Android di eseguire il processo per garantire la massima efficienza del dispositivo. Ad esempio, è possibile che le richieste di rete vengano eseguite in batch in modo che vengano eseguite contemporaneamente per sfruttare al massimo l'overhead richiesto per la rete.
2. L' **incapsulamento del lavoro** &ndash; il codice per eseguire le operazioni in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sarà relativamente semplice da ripianificare se il lavoro non viene completato per alcuni motivo.

L'utilità di pianificazione dei processi Android è un framework integrato nel sistema operativo Android che fornisce un'API Fluent per semplificare la pianificazione del lavoro in background.  L'utilità di pianificazione dei processi Android è costituita dai tipi seguenti:

- Il `Android.App.Job.JobScheduler` è un servizio di sistema usato per pianificare, eseguire e, se necessario, annullare i processi per conto di un'applicazione Android.
- Un `Android.App.Job.JobService` è una classe astratta che deve essere estesa con la logica che eseguirà il processo sul thread principale dell'applicazione. Ciò significa che il `JobService` è responsabile della modalità di esecuzione del lavoro in modo asincrono.
- Un oggetto `Android.App.Job.JobInfo` include i criteri per guidare Android quando il processo deve essere eseguito.

Per pianificare il lavoro con l'utilità di pianificazione dei processi Android, un'applicazione Novell. Android deve incapsulare il codice in una classe che estende la classe `JobService`. `JobService` dispone di tre metodi del ciclo di vita che possono essere chiamati durante il processo:

- **bool OnStartJob (parametri JobParameters)** &ndash; questo metodo viene chiamato dal `JobScheduler` per eseguire il lavoro e viene eseguito sul thread principale dell'applicazione. È responsabilità del `JobService` eseguire in modo asincrono il lavoro e `true` se il lavoro rimane o `false` se il lavoro viene eseguito.
    
    Quando il `JobScheduler` chiama questo metodo, richiede e mantiene un Wakelock da Android per la durata del processo. Al termine del processo, è responsabilità del `JobService` indicare il `JobScheduler` di questo fatto chiamando il metodo `JobFinished` (descritto di seguito).

- **JobFinished (JobParameters Parameters, bool needsReschedule)** &ndash; questo metodo deve essere chiamato dal `JobService` per indicare al `JobScheduler` che il lavoro è stato eseguito. Se `JobFinished` non viene chiamato, il `JobScheduler` non rimuoverà il Wakelock, causando un esaurimento della batteria superfluo. 

- **bool OnStopJob (parametri JobParameters)** &ndash; questa operazione viene chiamata quando il processo viene arrestato in modo anomalo da Android. Deve restituire `true` se il processo deve essere ripianificato in base ai criteri di ripetizione dei tentativi (descritti più avanti in modo più dettagliato).

È possibile specificare _vincoli_ o _trigger_ che controlleranno quando un processo può o deve essere eseguito. Ad esempio, è possibile vincolare un processo in modo che venga eseguito solo quando il dispositivo viene caricato o per avviare un processo quando viene acquisita un'immagine.

In questa guida verrà illustrato in dettaglio come implementare una classe di `JobService` e pianificarla con l'`JobScheduler`.

## <a name="requirements"></a>Requisiti

L'utilità di pianificazione dei processi Android richiede il livello di API Android 21 (Android 5,0) o versione successiva. 

## <a name="using-the-android-job-scheduler"></a>Uso dell'utilità di pianificazione dei processi Android

Per l'uso dell'API JobScheduler Android sono disponibili tre passaggi:

1. Implementare un tipo JobService per incapsulare il lavoro.
2. Usare un oggetto `JobInfo.Builder` per creare l'oggetto `JobInfo` che conterrà i criteri per l'`JobScheduler` per eseguire il processo. 
3. Pianificare il processo utilizzando `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementare una JobService

Tutto il lavoro eseguito dalla libreria dell'utilità di pianificazione dei processi Android deve essere eseguito in un tipo che estende la classe astratta `Android.App.Job.JobService`. La creazione di un `JobService` è molto simile alla creazione di una `Service` con il Framework Android: 

1. Estendere la classe `JobService`.
2. Decorare la sottoclasse con il `ServiceAttribute` e impostare il parametro `Name` su una stringa costituita dal nome del pacchetto e dal nome della classe (vedere l'esempio seguente).
3. Impostare la proprietà `Permission` della `ServiceAttribute` sul `android.permission.BIND_JOB_SERVICE`di stringa.
4. Eseguire l'override del metodo `OnStartJob`, aggiungendo il codice per eseguire il lavoro. Android richiamerà questo metodo sul thread principale dell'applicazione per eseguire il processo. Per evitare di bloccare l'applicazione, è necessario eseguire un lavoro che comporterà più tempo.
5. Al termine del lavoro, il `JobService` deve chiamare il metodo `JobFinished`. Questo metodo rappresenta il modo in cui `JobService` indica al `JobScheduler` che il lavoro è stato eseguito. La mancata chiamata `JobFinished` comporterà la `JobService` l'inserimento di richieste non necessarie sul dispositivo, riducendo la durata della batteria. 
6. È consigliabile anche eseguire l'override del metodo `OnStopJob`. Questo metodo viene chiamato da Android quando il processo viene arrestato prima del completamento e fornisce all'`JobService` la possibilità di eliminare correttamente le risorse. Questo metodo deve restituire `true` se è necessario ripianificare il processo oppure `false` se non è auspicabile eseguire di nuovo il processo.

Il codice seguente è un esempio della `JobService` più semplice per un'applicazione, usando TPL per eseguire in modo asincrono alcune operazioni:

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

Le applicazioni Novell. Android non creano direttamente un'istanza di un `JobService`, bensì passano un oggetto `JobInfo` al `JobScheduler`. Il `JobScheduler` creerà un'istanza dell'oggetto `JobService` richiesto, pianificando ed eseguendo il `JobService` in base ai metadati nel `JobInfo`. Un oggetto `JobInfo` deve contenere le seguenti informazioni:

- **JobId** &ndash; si tratta di un valore `int` usato per identificare un processo al `JobScheduler`. Il riutilizzo di questo valore aggiornerà eventuali processi esistenti. Il valore deve essere univoco per l'applicazione. 
- **JobService** &ndash; questo parametro è un `ComponentName` che identifica in modo esplicito il tipo che il `JobScheduler` deve usare per eseguire un processo. 

Questo metodo di estensione illustra come creare una `JobInfo.Builder` con un `Context`Android, ad esempio un'attività:

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

Una potente funzionalità dell'utilità di pianificazione dei processi Android è la possibilità di controllare quando viene eseguito un processo o in quali condizioni può essere eseguito un processo. Nella tabella seguente vengono descritti alcuni dei metodi `JobInfo.Builder` che consentono a un'applicazione di influenzare quando è possibile eseguire un processo:  

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

Il `SetBackoffCriteria` fornisce alcune indicazioni sul tempo di attesa del `JobScheduler` prima di ritentare l'esecuzione di un processo. Sono disponibili due parti per i criteri backoff: un ritardo in millisecondi (valore predefinito di 30 secondi) e il tipo di back off da usare (a volte definito _criterio backoff_ o i _criteri di ripetizione dei tentativi_). I due criteri sono incapsulati nella `Android.App.Job.BackoffPolicy` enum:

- `BackoffPolicy.Exponential` &ndash; un criterio di backoff esponenziale aumenterà in modo esponenziale il valore iniziale di backoff dopo ogni errore. La prima volta che si verifica un errore in un processo, la libreria attenderà l'intervallo iniziale specificato prima di ripianificare il processo, ad esempio 30 secondi. La seconda volta che il processo non riesce, la libreria attenderà almeno 60 secondi prima di provare a eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria attenderà 120 secondi e così via. Questo è il valore predefinito.
- `BackoffPolicy.Linear` &ndash; questa strategia è una backoff lineare che il processo deve essere ripianificato per l'esecuzione a intervalli prestabiliti (fino a quando non ha esito positivo). Il backoff lineare è più adatto per il lavoro che deve essere completato il prima possibile o per i problemi che si risolveranno rapidamente. 

Per altri dettagli su come creare un oggetto `JobInfo`, vedere [la documentazione di Google relativa alla classe `JobInfo.Builder`](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passaggio di parametri a un processo tramite JobInfo

I parametri vengono passati a un processo creando un `PersistableBundle` passato insieme al metodo `Job.Builder.SetExtras`:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

È possibile accedere al `PersistableBundle` dalla proprietà `Android.App.Job.JobParameters.Extras` nel metodo `OnStartJob` di un `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Per pianificare un processo, un'applicazione Novell. Android otterrà un riferimento al servizio di sistema `JobScheduler` e chiamerà il metodo `JobScheduler.Schedule` con l'oggetto `JobInfo` creato nel passaggio precedente. `JobScheduler.Schedule` restituirà immediatamente uno dei due valori integer:

- **JobScheduler. ResultSuccess** &ndash; il processo è stato pianificato correttamente. 
- **JobScheduler. ResultFailure** &ndash; non è stato possibile pianificare il processo. Questa situazione è in genere causata da parametri `JobInfo` in conflitto.

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

È possibile annullare tutti i processi pianificati o solo un singolo processo usando il metodo `JobsScheduler.CancelAll()` o il metodo `JobScheduler.Cancel(jobId)`:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come usare l'utilità di pianificazione dei processi Android per eseguire in modo intelligente il lavoro in background. È stato illustrato come incapsulare il lavoro da eseguire come `JobService` e come usare i `JobScheduler` per pianificare il lavoro, specificando i criteri con una `JobTrigger` e come gestire gli errori con un `RetryStrategy`.

## <a name="related-links"></a>Collegamenti correlati

- [Pianificazione intelligente del processo](https://developer.android.com/topic/performance/scheduling.html)
- [Informazioni di riferimento sull'API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Pianificazione di processi come un Pro con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Ottimizzazioni della memoria e della batteria Android-Google I/O 2016 (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
