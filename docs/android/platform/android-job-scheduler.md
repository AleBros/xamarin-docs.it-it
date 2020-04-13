---
title: Utilità di pianificazione di processo Android
description: Questa guida illustra come pianificare il lavoro in background usando l'API Utilità di pianificazione processi Android.This guide discusses how to schedule background work using the Android Job Scheduler API.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 10d2ae6ac35f02d75ef6e04a0531ec3f5dafd668
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292160"
---
# <a name="android-job-scheduler"></a>Utilità di pianificazione di processo Android

_Questa guida illustra come pianificare il lavoro in background usando l'API dell'utilità di pianificazione dei processi Android, disponibile nei dispositivi Android che eseguono Android 5.0 (livello API 21) e versioni successive._

## <a name="overview"></a>Panoramica 

Uno dei modi migliori per mantenere un'applicazione Android reattiva all'utente consiste nel garantire che il lavoro complesso o a esecuzione prolungata venga eseguito in background. Tuttavia, è importante che il lavoro in background non influisca negativamente sull'esperienza dell'utente con il dispositivo. 

Ad esempio, un processo in background potrebbe eseguire il polling di un sito Web ogni tre o quattro minuti per eseguire una query per le modifiche a un determinato set di dati. Questo sembra benigno, tuttavia avrebbe un impatto disastroso sulla durata della batteria. L'applicazione riattiverà ripetutamente il dispositivo, eleverà la CPU a uno stato di alimentazione superiore, accenderà le radio, effettuerà le richieste di rete e quindi elaborazione dei risultati. Peggiora perché il dispositivo non si spegne immediatamente e ritorna allo stato di inattività a basso consumo. Il lavoro in background non programmato correttamente può inavvertitamente mantenere il dispositivo in uno stato con requisiti di alimentazione inutili ed eccessivi. Questa attività apparentemente innocente (polling di un sito web) renderà il dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le seguenti API per facilitare l'esecuzione di lavoro in background, ma da soli non sono sufficienti per la pianificazione intelligente dei processi. 

- **[I servizi intento](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; di intenti sono ideali per l'esecuzione del lavoro, tuttavia non forniscono alcun modo per pianificare il lavoro.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Queste API consentono solo di pianificare il lavoro, ma non forniscono alcun modo per eseguire effettivamente il lavoro. Inoltre, il AlarmManager consente solo vincoli basati sul tempo, il che significa generare un allarme in un determinato momento o dopo un determinato periodo di tempo è trascorso. 
- **[Ricevitori](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; di trasmissione Un'app Android può configurare ricevitori di trasmissione per eseguire operazioni in risposta a eventi o finalità a livello di sistema. Tuttavia, i ricevitori broadcast non forniscono alcun controllo su quando il processo deve essere eseguito. Anche le modifiche nel sistema operativo Android limiteranno quando i ricevitori di trasmissione funzioneranno, o il tipo di lavoro a cui possono rispondere. 

Esistono due caratteristiche chiave per eseguire in modo efficiente il lavoro in background (talvolta indicato come un lavoro in _background_ o un _lavoro_):

1. **Pianificazione intelligente del lavoro** &ndash; È importante che quando un'applicazione sta facendo il lavoro in background che lo fa come un buon cittadino. In teoria, l'applicazione non deve richiedere l'esecuzione di un processo. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per quando il processo può essere eseguito e quindi pianificare il processo con il sistema operativo che eseguirà il lavoro quando vengono soddisfatte le condizioni. Ciò consente ad Android di eseguire il processo per garantire la massima efficienza sul dispositivo. Ad esempio, le richieste di rete possono essere raggruppate in batch per l'esecuzione contemporanea per sfruttare al massimo l'overhead associato alla rete.
2. **Incapsulamento del lavoro** &ndash; Il codice per eseguire il lavoro in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sarà relativamente facile da ripianificare se il lavoro non viene completato per qualche motivo.

L'utilità di pianificazione dei processi Android è un framework integrato nel sistema operativo Android che fornisce un'API fluente per semplificare la pianificazione del lavoro in background.  L'utilità di pianificazione dei processi Android è costituita dai seguenti tipi:

- Il `Android.App.Job.JobScheduler` è un servizio di sistema che viene utilizzato per pianificare, eseguire e, se necessario, annullare, processi per conto di un'applicazione Android.The is a system service that is used to schedule, execute, and if necessary cancel, jobs for behalf of an Android application.
- Un `Android.App.Job.JobService` è una classe astratta che deve essere estesa con la logica che eseguirà il processo nel thread principale dell'applicazione. Ciò significa `JobService` che l'oggetto è responsabile del modo in cui il lavoro deve essere eseguito in modo asincrono.
- Un `Android.App.Job.JobInfo` oggetto contiene i criteri per guidare Android quando il processo deve essere eseguito.

Per pianificare il lavoro con l'utilità di pianificazione dei processi Android, un'applicazione Xamarin.Android deve incapsulare il codice in una classe che estende la `JobService` classe. `JobService`dispone di tre metodi del ciclo di vita che possono essere chiamati durante la durata del processo:

- **bool OnStartJob(JobParameters parametri)** &ndash; Questo metodo `JobScheduler` viene chiamato da per eseguire il lavoro ed viene eseguito sul thread principale dell'applicazione. È responsabilità del `JobService` per eseguire in modo `true` asincrono il lavoro `false` e tornare se c'è lavoro rimanente, o se il lavoro è fatto.
    
    Quando `JobScheduler` chiama questo metodo, richiederà e conserverà un wakelock da Android per la durata del processo. Al termine del processo, è responsabilità di indicare `JobService` il `JobScheduler` `JobFinished` fatto di questo fatto chiamando il metodo (descritto di seguito).

- **JobFinished(JobParameters parameters, bool needsReschedule)** &ndash; Questo metodo `JobService` deve `JobScheduler` essere chiamato da parte di per indicare che il lavoro è stato eseguito. Se `JobFinished` non viene `JobScheduler` chiamato, il non rimuoverà il wakelock, causando inutili scarico della batteria. 

- **bool OnStopJob(JobParameters parameters)** &ndash; Viene chiamato quando il processo viene interrotto prematuramente da Android. Deve restituire `true` se il processo deve essere riprogrammato in base ai criteri di ripetizione dei tentativi (illustrati di seguito in modo più dettagliato).

È possibile specificare _vincoli_ o _trigger_ che controlleranno quando un processo può o deve essere eseguito. Ad esempio, è possibile vincolare un processo in modo che venga eseguito solo quando il dispositivo è in carica o per avviare un processo quando viene scattata una foto.

Questa guida verrà illustrato in `JobService` dettaglio come implementare una classe e pianificarla con il `JobScheduler`file .

## <a name="requirements"></a>Requisiti

L'utilità di pianificazione dei processi Android richiede android API livello 21 (Android 5.0) o superiore. 

## <a name="using-the-android-job-scheduler"></a>Utilizzo dell'utilità di pianificazione dei processi AndroidUsing the Android Job Scheduler

Esistono tre passaggi per l'utilizzo dell'API Android JobScheduler:There are three steps for using the Android JobScheduler API:

1. Implementare un tipo JobService per incapsulare il lavoro.
2. Utilizzare `JobInfo.Builder` un oggetto `JobInfo` per creare l'oggetto `JobScheduler` che conterrà i criteri per l'esecuzione del processo. 
3. Pianificare il `JobScheduler.Schedule`processo utilizzando .

### <a name="implement-a-jobservice"></a>Implementare un JobServiceImplement a JobService

Tutto il lavoro eseguito dalla libreria di utilità di `Android.App.Job.JobService` pianificazione processi Android deve essere eseguito in un tipo che estende la classe astratta. Creazione `JobService` di un è `Service` molto simile alla creazione di un con il framework Android:Creating a is very similar to creating a with the Android framework: 

1. Estendi la `JobService` classe.
2. Decorare la sottoclasse `ServiceAttribute` con `Name` il e impostare il parametro su una stringa costituita dal nome del pacchetto e dal nome della classe (vedere l'esempio seguente).
3. Impostare `Permission` la `ServiceAttribute` proprietà su `android.permission.BIND_JOB_SERVICE` su .
4. Eseguire `OnStartJob` l'override del metodo, aggiungendo il codice per eseguire il lavoro. Android richiamerà questo metodo sul thread principale dell'applicazione per eseguire il processo. Lavoro che richiederà più tempo che alcuni millisecondi devono essere eseguiti su un thread per evitare di bloccare l'applicazione.
5. Al termine del lavoro, `JobService` è `JobFinished` necessario chiamare il metodo . Questo metodo `JobService` è `JobScheduler` il modo in cui indica che il lavoro viene eseguito. La mancata chiamata `JobFinished` `JobService` comporterà l'utilizzo di richieste non necessarie sul dispositivo, riducendo la durata della batteria. 
6. È consigliabile eseguire anche `OnStopJob` l'override del metodo. Questo metodo viene chiamato da Android quando il processo viene `JobService` arrestato prima che venga completato e offre l'opportunità di eliminare correttamente le risorse. Questo metodo `true` deve restituire se è necessario ripianificare il processo o `false` se non è opportuno rieseguire il processo.

Il codice seguente è un `JobService` esempio del più semplice per un'applicazione, utilizzando la proprietà TPL per eseguire alcune operazioni in modo asincrono:The following code is an example of the simplest for an application, using the TPL to asynchronously perform some work:

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

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Creazione di un JobInfo per pianificare un processoCreating a JobInfo to schedule a job

Le applicazioni Xamarin.Android `JobService` non fanno un'istanza `JobInfo` diretta, `JobScheduler`ma passeranno un oggetto all'oggetto . Verrà `JobScheduler` creata un'istanza dell'oggetto `JobService` `JobService` richiesto, pianificando `JobInfo`ed eseguendo l'oggetto in base ai metadati nel file . Un `JobInfo` oggetto deve contenere le seguenti informazioni:

- **JobId** &ndash; si `int` tratta di un valore utilizzato `JobScheduler`per identificare un processo per l'oggetto . Riutilizzando questo valore verranno aggiornati tutti i processi esistenti. Il valore deve essere univoco per l'applicazione. 
- **JobService** &ndash; questo parametro è un `ComponentName` che `JobScheduler` identifica in modo esplicito il tipo che deve essere utilizzato per eseguire un processo. 

Questo metodo di estensione `JobInfo.Builder` viene illustrato `Context`come creare un con un Android , ad esempio un'attività:This extension method demonstrates how to create a with an Android , such as an Activity:

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

Una funzionalità potente di Android Job Scheduler è la possibilità di controllare quando un processo viene eseguito o in quali condizioni un processo può essere eseguito. La tabella seguente descrive alcuni `JobInfo.Builder` dei metodi che consentono a un'app di influenzare l'esecuzione di un processo:  

|  Metodo | Descrizione   |
|---|---|
| `SetMinimumLatency`  | Specifica che deve essere osservato un ritardo (in millisecondi) prima dell'esecuzione di un processo. |
| `SetOverridingDeadline`  | Dichiara che il processo deve essere eseguito prima che sia trascorso questo tempo (in millisecondi). |
| `SetRequiredNetworkType`  | Specifica i requisiti di rete per un processo. |
| `SetRequiresBatteryNotLow` | Il processo può essere eseguito solo quando il dispositivo non visualizza un avviso di "batteria scarica" per l'utente. |
| `SetRequiresCharging` | Il processo può essere eseguito solo quando la batteria è in carica. |
| `SetDeviceIdle` | Il processo verrà eseguito quando il dispositivo è occupato. |
| `SetPeriodic` | Specifica che il processo deve essere eseguito regolarmente. |
| `SetPersisted` | Il lavoro dovrebbe perisist tra i riavvii del dispositivo. | 

L'oggetto `SetBackoffCriteria` fornisce alcune `JobScheduler` indicazioni su quanto tempo l'attesa deve essere attendere prima di tentare di eseguire nuovamente un processo. Esistono due parti dei criteri di backoff: un ritardo in millisecondi (valore predefinito di 30 secondi) e il tipo di back off che deve essere utilizzato (talvolta definito _criterio di backoff_ o _criterio di ripetizione dei tentativi)._ I due criteri sono `Android.App.Job.BackoffPolicy` incapsulati nell'enumerazione:

- `BackoffPolicy.Exponential`&ndash; Un criterio di backoff esponenziale aumenterà il valore di backoff iniziale in modo esponenziale dopo ogni errore. La prima volta che un processo ha esito negativo, la libreria attenderà l'intervallo iniziale specificato prima di ripianificare il processo, ad esempio 30 secondi. La seconda volta che il processo ha esito negativo, la libreria attenderà almeno 60 secondi prima di tentare di eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria attenderà 120 secondi e così via. Si tratta del valore predefinito.
- `BackoffPolicy.Linear`&ndash; Questa strategia è un backoff lineare che il processo deve essere riprogrammato per l'esecuzione a intervalli prestabiliti (fino a quando non riesce). Il backoff lineare è più adatto per lavori che devono essere completati il prima possibile o per problemi che si risolveranno rapidamente. 

Per maggiori dettagli `JobInfo` sulla creazione di un oggetto, leggi [la documentazione di Google per la `JobInfo.Builder` classe](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passaggio di parametri a un processo tramite JobInfoPassing parameters to a job via the JobInfo

I parametri vengono passati `PersistableBundle` a un processo `Job.Builder.SetExtras` creando un oggetto che viene passato insieme al metodo :

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

È `PersistableBundle` possibile accedervi `Android.App.Job.JobParameters.Extras` dalla `OnStartJob` proprietà `JobService`nel metodo di :

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Per pianificare un processo, un'applicazione Xamarin.Android otterrà un riferimento al servizio di `JobScheduler` sistema e chiamerà il `JobScheduler.Schedule` metodo con l'oggetto `JobInfo` creato nel passaggio precedente. `JobScheduler.Schedule`verrà restituito immediatamente con uno dei due due valori interi:

- **JobScheduler.ResultSuccess** &ndash; Il processo è stato pianificato correttamente. 
- **Impossibile pianificare** &ndash; il processo. Ciò è in `JobInfo` genere causato da parametri in conflitto.

Questo codice è un esempio di pianificazione di un processo e notifica all'utente i risultati del tentativo di pianificazione:

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

È possibile annullare tutti i processi pianificati o solo `JobsScheduler.CancelAll()` un singolo `JobScheduler.Cancel(jobId)` processo utilizzando il metodo o il metodo:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Riepilogo

Questa guida illustra come usare l'utilità di pianificazione dei processi di Android per eseguire il lavoro in modo intelligente in background. Viene illustrato come incapsulare il `JobService` lavoro da eseguire `JobScheduler` come un e come utilizzare `JobTrigger` il per pianificare tale lavoro, specificando i criteri con un e come gli errori devono essere gestiti con un `RetryStrategy`oggetto .

## <a name="related-links"></a>Collegamenti correlati

- [Pianificazione intelligente dei processi](https://developer.android.com/topic/performance/scheduling.html)
- [Informazioni di riferimento sull'API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Pianificazione di processi come un professionista con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Ottimizzazioni della batteria e della memoria Android - Google I/O 2016 (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
