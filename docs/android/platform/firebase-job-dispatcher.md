---
title: Firebase Job Dispatcher
description: Questa guida illustra come pianificare il lavoro in background usando la libreria del dispatcher di processi di Firebase da Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: cb7e8aaca13405aedd422288421d497653ddbfe8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761197"
---
# <a name="firebase-job-dispatcher"></a>Firebase Job Dispatcher

_Questa guida illustra come pianificare il lavoro in background usando la libreria del dispatcher di processi di Firebase da Google._

## <a name="overview"></a>Panoramica

Uno dei modi migliori per garantire che un'applicazione Android sia in grado di rispondere all'utente è garantire che il lavoro complesso o a esecuzione prolungata venga eseguito in background. Tuttavia, è importante che il lavoro in background non influisca negativamente sull'esperienza dell'utente con il dispositivo. 

Un processo in background, ad esempio, può eseguire il polling di un sito Web ogni tre o quattro minuti per cercare le modifiche apportate a un determinato set di dati Questa situazione sembra benigna, ma potrebbe avere un effetto disastroso sulla durata della batteria. L'applicazione riattiverà ripetutamente il dispositivo, eleva la CPU a uno stato di alimentazione superiore, accende le radio, effettua le richieste di rete e quindi elabora i risultati. Peggiora perché il dispositivo non si spegne immediatamente e torna allo stato di inattività a basso consumo. Il lavoro in background pianificato in modo non corretto può inavvertitamente impedire che il dispositivo si trovi in uno stato con requisiti di alimentazione superflui e Questa attività apparentemente innocente (polling di un sito Web) rende il dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le API seguenti che consentono di eseguire il lavoro in background, ma non sono sufficienti per la pianificazione intelligente dei processi. 

- **[Servizi Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; I servizi per finalità sono ottimi per l'esecuzione del lavoro, ma non forniscono alcun modo per pianificare il lavoro.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Queste API consentono solo la pianificazione del lavoro, ma non forniscono alcun modo per eseguire effettivamente il lavoro. Inoltre, AlarmManager consente solo vincoli basati sul tempo, il che significa generare un allarme in un determinato momento o dopo che è trascorso un determinato periodo di tempo. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule è un'ottima API che interagisce con il sistema operativo per pianificare i processi. Tuttavia, è disponibile solo per le app Android destinate a livello API 21 o versione successiva. 
- **[Ricevitori broadcast](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Un'app Android può configurare i ricevitori di trasmissioni per eseguire il lavoro in risposta a eventi a livello di sistema o Intent. Tuttavia, i ricevitori di trasmissioni non forniscono alcun controllo sul momento in cui il processo deve essere eseguito. Inoltre, le modifiche apportate al sistema operativo Android verranno limitate quando i ricevitori di trasmissioni funzioneranno o i tipi di lavoro a cui possono rispondere. 

Sono disponibili due funzionalità principali per eseguire in modo efficiente il lavoro in background (talvolta definito _processo in background_ o _processo_):

1. **Pianificazione intelligente del lavoro** &ndash; È importante che, quando un'applicazione esegue il lavoro in background, in quanto è un valido cittadino. Idealmente, l'applicazione non deve richiedere l'esecuzione di un processo. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per il momento in cui il processo può essere eseguito, quindi pianificare il lavoro da eseguire quando vengono soddisfatte le condizioni. Ciò consente ad Android di eseguire in modo intelligente il lavoro. Ad esempio, è possibile che le richieste di rete vengano eseguite in batch in modo che vengano eseguite contemporaneamente per sfruttare al massimo l'overhead richiesto per la rete.
2. **Incapsulamento del lavoro** &ndash; Il codice per eseguire le operazioni in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sarà relativamente semplice da ripianificare se il lavoro non viene completato per qualche motivo.

Il dispatcher del processo Firebase è una libreria di Google che fornisce un'API Fluent per semplificare la pianificazione del lavoro in background. È destinato a essere la sostituzione di Google Cloud Manager. Il dispatcher del processo Firebase è costituito dalle API seguenti:

- Un `Firebase.JobDispatcher.JobService` oggetto è una classe astratta che deve essere estesa con la logica che verrà eseguita nel processo in background.
- Un `Firebase.JobDispatcher.JobTrigger` oggetto dichiara quando il processo deve essere avviato. Questa operazione viene in genere espressa come una finestra temporale, ad esempio, attendere almeno 30 secondi prima di avviare il processo, ma eseguire il processo entro 5 minuti.
- Un `Firebase.JobDispatcher.RetryStrategy` oggetto contiene informazioni sulle operazioni che devono essere eseguite quando un processo non viene eseguito correttamente. La strategia di ripetizione dei tentativi specifica il tempo di attesa prima di tentare di eseguire nuovamente il processo. 
- Un `Firebase.JobDispatcher.Constraint` è un valore facoltativo che descrive una condizione che deve essere soddisfatta prima che il processo possa essere eseguito, ad esempio se il dispositivo si trova in una rete non a consumo o si sta caricando.
- È un'API che unifica le API precedenti in a un'unità di lavoro che può essere pianificata `JobDispatcher`da. `Firebase.JobDispatcher.Job` La `Job.Builder` classe viene utilizzata per creare un'istanza `Job`di un oggetto.
- Un `Firebase.JobDispatcher.JobDispatcher` usa le tre API precedenti per pianificare il lavoro con il sistema operativo e per fornire un modo per annullare i processi, se necessario.

Per pianificare il lavoro con il dispatcher del processo Firebase, un'applicazione Novell. Android deve incapsulare il codice in un tipo `JobService` che estende la classe. `JobService`dispone di tre metodi del ciclo di vita che possono essere chiamati durante il processo:

- **`bool OnStartJob(IJobParameters parameters)`** &ndash; Questo metodo è il punto in cui si verificherà il lavoro e deve essere sempre implementato. Viene eseguito nel thread principale. Questo metodo restituirà `true` se il lavoro rimane o `false` se il lavoro viene eseguito. 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; Questo metodo viene chiamato quando il processo viene arrestato per qualche motivo. Deve restituire `true` se il processo deve essere ripianificato per un momento successivo.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** Questo metodo viene chiamato quando l' `JobService` oggetto ha terminato un lavoro asincrono. &ndash; 

Per pianificare un processo, l'applicazione creerà un'istanza di `JobDispatcher` un oggetto. Viene quindi usato `Job.Builder` un oggetto per creare un `Job` oggetto, fornito a `JobDispatcher` che tenterà di pianificare l'esecuzione del processo.

Questa guida illustra come aggiungere il dispatcher del processo di Firebase a un'applicazione Novell. Android e usarlo per pianificare il lavoro in background.

## <a name="requirements"></a>Requisiti

Il dispatcher del processo Firebase richiede il livello 9 dell'API Android o versione successiva. La libreria del dispatcher del processo Firebase si basa su alcuni componenti forniti da Google Play Services; nel dispositivo deve essere installato Google Play Services.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso della libreria del dispatcher di processi Firebase in Novell. Android

Per iniziare a usare il dispatcher del processo Firebase, aggiungere prima il [pacchetto NuGet Novell. Firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al progetto Novell. Android. Eseguire una ricerca in Gestione pacchetti NuGet per il pacchetto **Novell. Firebase. JobDispatcher** (che è ancora in versione non definitiva).

Dopo aver aggiunto la libreria del dispatcher dei processi Firebase `JobService` , creare una classe e quindi pianificarne l'esecuzione con un' `FirebaseJobDispatcher`istanza del.

### <a name="creating-a-jobservice"></a>Creazione di un JobService

Tutte le operazioni eseguite dalla libreria del dispatcher dei processi Firebase devono essere eseguite in un tipo che `Firebase.JobDispatcher.JobService` estende la classe astratta. La creazione `JobService` di un oggetto è molto simile `Service` alla creazione di una con il Framework Android: 

1. Estendi la `JobService` classe
2. Decorare la sottoclasse con `ServiceAttribute`. Sebbene non sia strettamente obbligatorio, è consigliabile impostare in modo esplicito `Name` il parametro per facilitare il `JobService`debug di. 
3. Aggiungere un `IntentFilter` oggetto per `JobService` dichiarare in **file AndroidManifest. XML**. Questa operazione aiuterà inoltre la libreria del dispatcher del processo Firebase a individuare `JobService`e richiamare.

Il codice seguente è un esempio del più semplice `JobService` per un'applicazione, usando TPL per eseguire in modo asincrono alcune operazioni:

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>Creazione di un FirebaseJobDispatcher

Prima di poter pianificare qualsiasi lavoro, è necessario creare un `Firebase.JobDispatcher.FirebaseJobDispatcher` oggetto. È responsabile della pianificazione di un oggetto `JobService`. `FirebaseJobDispatcher` Il frammento di codice seguente è un modo per creare un'istanza `FirebaseJobDispatcher`di: 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Nel frammento di codice precedente, `GooglePlayDriver` la classe è che `FirebaseJobDispatcher` consente di interagire con alcune delle API di pianificazione in Google Play Services sul dispositivo. Il parametro `context` è qualsiasi Android `Context`, ad esempio un'attività. Attualmente è l'unica `IDriver` implementazione nella libreria del dispatcher del processo Firebase. `GooglePlayDriver` 

Il binding Novell. Android per il dispatcher del processo Firebase fornisce un metodo di estensione per `FirebaseJobDispatcher` creare un `Context`oggetto da: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una volta creata un'istanza di, è possibile creare un oggetto `Job` ed `JobService` eseguire il codice nella classe. `FirebaseJobDispatcher` Viene creato da un `Job.Builder` oggetto e verrà illustrato nella sezione successiva. `Job`

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creazione di un Firebase. JobDispatcher. job con Job. Builder

La `Firebase.JobDispatcher.Job` classe è responsabile dell'incapsulamento dei metadati necessari per `JobService`eseguire. Un`Job` oggetto contiene informazioni quali tutti i vincoli che devono essere soddisfatti prima di poter eseguire il processo, `Job` se è ricorrente o qualsiasi trigger che provocherà l'esecuzione del processo.  `Job` Come minimo, un deve avere un _tag_ (una stringa univoca che identifica `FirebaseJobDispatcher`il processo a) e il tipo dell'oggetto `JobService` che deve essere eseguito. Il dispatcher del processo Firebase creerà un'istanza `JobService` di quando è il momento di eseguire il processo.  Viene `Job` creato un oggetto utilizzando un'istanza `Firebase.JobDispatcher.Job.JobBuilder` della classe. 

Il frammento di codice seguente è l'esempio più semplice per creare un `Job` usando il binding Novell. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

In `Job.Builder` vengono eseguiti alcuni controlli di convalida di base sui valori di input per il processo. Se non è possibile per la `Job.Builder` creazione di un `Job`oggetto, verrà generata un'eccezione.  Creerà un oggetto `Job` con i valori predefiniti seguenti: `Job.Builder`

- La `Job` _durata_ di un periodo di tempo (il tempo pianificato per l'esecuzione) è solo fino al riavvio &ndash; del dispositivo `Job` dopo il riavvio del dispositivo.
- Un `Job` oggetto non è &ndash; ricorrente e verrà eseguito una sola volta.
- Un `Job` verrà pianificato per l'esecuzione appena possibile.
- La strategia di ripetizione dei tentativi `Job` predefinita per a prevede l'uso di un _backoff esponenziale_ (illustrato più dettagliatamente nella sezione [impostazione di un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Dopo la `Job`creazione di, è necessario pianificarlo `FirebaseJobDispatcher` con prima che venga eseguito. Esistono due metodi per pianificare un oggetto `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

Il valore restituito da `FirebaseJobDispatcher.Schedule` sarà uno dei valori interi seguenti:

- `FirebaseJobDispatcher.ScheduleResultSuccess`La pianificazione di `Job` è stata completata. &ndash;
- `FirebaseJobDispatcher.ScheduleResultUnknownError`Si è verificato un problema sconosciuto che `Job` ha impedito la pianificazione di. &ndash;
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`È stato `IDriver` usato un oggetto non `IDriver` valido o è stato non disponibile. &ndash; 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`L' oggetto`Trigger` non è supportato. &ndash;
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash; Il servizio non è configurato correttamente o non è disponibile.

### <a name="configuring-a-job"></a>Configurazione di un processo

È possibile personalizzare un processo. Di seguito sono riportati alcuni esempi di come è possibile personalizzare un processo:

- [Passaggio di parametri a un processo](#Passing_Parameters_to_a_Job) &ndash; Un`Job` potrebbe richiedere valori aggiuntivi per eseguire il proprio lavoro, ad esempio il download di un file.
- [Imposta vincoli](#Setting_Constraints) &ndash; Potrebbe essere necessario eseguire un processo solo quando vengono soddisfatte determinate condizioni. Ad esempio, eseguire solo un `Job` quando il dispositivo viene caricato. 
- [Specificare `Job` quando deve essere eseguito](#Setting_Job_Triggers) &ndash; il dispatcher del processo Firebase consente alle applicazioni di specificare l'ora in cui deve essere eseguito il processo.  
- [Dichiarare una strategia di ripetizione dei tentativi per i processi non riusciti](#Setting_a_RetryStrategy) Una strategia di _ripetizione dei tentativi_ fornisce `FirebaseJobDispatcher` indicazioni per le operazioni da `Jobs` eseguire in caso di esito negativo. &ndash; 

Ognuno di questi argomenti verrà illustrato in dettaglio nelle sezioni seguenti.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passaggio di parametri a un processo

`Bundle` I`Job.Builder.SetExtras` parametri vengono passati a un processo creando un oggetto che viene passato insieme al metodo:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

È `Bundle` possibile accedere `IJobParameters.Extras` alla proprietà nel `OnStartJob` metodo:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Impostazione di vincoli

I vincoli consentono di ridurre i costi o lo svuotamento della batteria sul dispositivo. La `Firebase.JobDispatcher.Constraint` classe definisce questi vincoli come valori integer:

- `Constraint.OnUnmeteredNetwork`&ndash; Eseguire il processo solo quando il dispositivo è connesso a una rete non a consumo. Questa operazione è utile per impedire all'utente di incorrere in addebiti per i dati.
- `Constraint.OnAnyNetwork`&ndash; Eseguire il processo in qualsiasi rete a cui è connesso il dispositivo. Se viene specificato insieme `Constraint.OnUnmeteredNetwork`a, questo valore ha la priorità.
- `Constraint.DeviceCharging`&ndash; Eseguire il processo solo quando il dispositivo viene caricato.

I vincoli vengono impostati con `Job.Builder.SetConstraint` il metodo: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger` Fornisce indicazioni al sistema operativo in merito all'avvio del processo. Un `JobTrigger` oggetto dispone di una _finestra in esecuzione_ che definisce un'ora pianificata `Job` per l'esecuzione di. La finestra di esecuzione ha un valore della finestra _iniziale_ e un valore della _finestra finale_ . La finestra iniziale indica il numero di secondi di attesa del dispositivo prima dell'esecuzione del processo e il valore della finestra finale è il numero massimo di secondi di attesa prima dell' `Job`esecuzione di. 

Un `JobTrigger` oggetto può essere creato con `Firebase.Jobdispatcher.Trigger.ExecutionWindow` il metodo.  Ad esempio `Trigger.ExecutionWindow(15,60)` , il processo deve essere eseguito tra 15 e 60 secondi da quando è pianificato. Il `Job.Builder.SetTrigger` metodo viene usato per 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

L'impostazione `JobTrigger` predefinita per un processo è rappresentata dal `Trigger.Now`valore, che specifica che un processo deve essere eseguito il prima possibile dopo essere stato pianificato.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Impostazione di un RetryStrategy

`Firebase.JobDispatcher.RetryStrategy` Viene usato per specificare la quantità di ritardo che un dispositivo deve usare prima di provare a eseguire nuovamente un processo non riuscito. Un `RetryStrategy` oggetto dispone di un _criterio_che definisce l'algoritmo di base temporale che verrà utilizzato per ripianificare il processo non riuscito e una finestra di esecuzione che specifica una finestra in cui il processo deve essere pianificato. Questa _finestra di ripianificazione_ è definita da due valori. Il primo valore è il numero di secondi di attesa prima di ripianificare il processo (il valore _iniziale di backoff_ ) e il secondo numero è il numero massimo di secondi prima che il processo venga eseguito (il valore _backoff massimo_ ). 

I due tipi di criteri di ripetizione sono identificati da questi valori int:

- `RetryStrategy.RetryPolicyExponential`Un criterio _backoff esponenziale_ aumenterà in modo esponenziale il valore iniziale di backoff dopo ogni errore. &ndash; La prima volta che si verifica un errore di un processo, la libreria attenderà l'intervallo di _initial specificato prima di &ndash; ripianificare l'esempio di processo 30 secondi. La seconda volta che il processo non riesce, la libreria attenderà almeno 60 secondi prima di provare a eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria attenderà 120 secondi e così via. Il valore `RetryStrategy` predefinito per la libreria del dispatcher del processo Firebase è `RetryStrategy.DefaultExponential` rappresentato dall'oggetto. Ha una backoff iniziale di 30 secondi e un backoff massimo di 3600 secondi.
- `RetryStrategy.RetryPolicyLinear`Questa strategia è un _backoff lineare_ che il processo deve essere ripianificato per l'esecuzione a intervalli prestabiliti (fino a quando non riesce). &ndash; Il backoff lineare è più adatto per il lavoro che deve essere completato il prima possibile o per i problemi che si risolveranno rapidamente. La libreria del dispatcher del processo Firebase `RetryStrategy.DefaultLinear` definisce un che ha una finestra di ripianificazione di almeno 30 secondi e fino a 3600 secondi.

È possibile definire un oggetto personalizzato `RetryStrategy` con il `FirebaseJobDispatcher.NewRetryStrategy` metodo. Sono necessari tre parametri:

1. `int policy``RetryStrategy.RetryPolicyLinear` `RetryStrategy.RetryPolicyExponential` `RetryStrategy` Il criterio è uno dei valori precedenti, o. &ndash;
2. `int initialBackoffSeconds`Il valore iniziale di backoff è un ritardo, in secondi, necessario prima di ritentare l'esecuzione del processo. &ndash; Il valore predefinito è 30 secondi. 
3. `int maximumBackoffSeconds`Il valore massimo di backoff dichiara il numero massimo di secondi di ritardo prima di provare a eseguire nuovamente il processo. &ndash; Il valore predefinito è 3600 secondi. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Annullamento di un processo

È possibile annullare tutti i processi pianificati o solo un singolo processo usando il `FirebaseJobDispatcher.CancelAll()` metodo o il `FirebaseJobDispatcher.Cancel(string)` metodo:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Entrambi i metodi restituiranno un valore integer:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash; Il processo è stato annullato.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash; Un errore ha impedito l'annullamento del processo.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`Non è in grado di annullare il processo perché non è disponibile `IDriver` alcun valido. `FirebaseJobDispatcher` &ndash;

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come usare il dispatcher del processo Firebase per eseguire in modo intelligente il lavoro in background. Viene illustrato come incapsulare il lavoro `JobService` da eseguire come e come `FirebaseJobDispatcher` utilizzare per pianificare il lavoro, specificando i criteri con un oggetto `JobTrigger` e la modalità di gestione degli errori con un oggetto `RetryStrategy`.

## <a name="related-links"></a>Collegamenti correlati

- [Novell. Firebase. JobDispatcher in NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [Firebase-job-Dispatcher su GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Pianificazione intelligente del processo](https://developer.android.com/topic/performance/scheduling.html)
- [Ottimizzazioni della memoria e della batteria Android-Google I/O 2016 (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
