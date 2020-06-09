---
title: Firebase Job Dispatcher
description: Questa guida illustra come pianificare il lavoro in background usando la libreria del dispatcher di processi di Firebase da Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 0ade609997e391e24d4a6da250172efa81a5d490
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571337"
---
# <a name="firebase-job-dispatcher"></a>Firebase Job Dispatcher

_Questa guida illustra come pianificare il lavoro in background usando la libreria del dispatcher di processi di Firebase da Google._

## <a name="overview"></a>Panoramica

Uno dei modi migliori per garantire che un'applicazione Android sia in grado di rispondere all'utente è garantire che il lavoro complesso o a esecuzione prolungata venga eseguito in background. Tuttavia, è importante che il lavoro in background non influisca negativamente sull'esperienza dell'utente con il dispositivo. 

Un processo in background, ad esempio, può eseguire il polling di un sito Web ogni tre o quattro minuti per cercare le modifiche apportate a un determinato set di dati Questa situazione sembra benigna, ma potrebbe avere un effetto disastroso sulla durata della batteria. L'applicazione riattiverà ripetutamente il dispositivo, eleva la CPU a uno stato di alimentazione superiore, accende le radio, effettua le richieste di rete e quindi elabora i risultati. Peggiora perché il dispositivo non si spegne immediatamente e torna allo stato di inattività a basso consumo. Il lavoro in background pianificato in modo non corretto può inavvertitamente impedire che il dispositivo si trovi in uno stato con requisiti di alimentazione superflui e Questa attività apparentemente innocente (polling di un sito Web) rende il dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le API seguenti che consentono di eseguire il lavoro in background, ma non sono sufficienti per la pianificazione intelligente dei processi. 

- **[Servizi](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; Intent I servizi per finalità sono ottimi per l'esecuzione del lavoro, ma non forniscono alcun modo per pianificare il lavoro.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Queste API consentono solo la pianificazione del lavoro, ma non forniscono alcun modo per eseguire effettivamente il lavoro. Inoltre, AlarmManager consente solo vincoli basati sul tempo, il che significa generare un allarme in un determinato momento o dopo che è trascorso un determinato periodo di tempo. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule è un'ottima API che interagisce con il sistema operativo per pianificare i processi. Tuttavia, è disponibile solo per le app Android destinate a livello API 21 o versione successiva. 
- **[Ricevitori broadcast](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Un'app Android può configurare i ricevitori di trasmissioni per eseguire il lavoro in risposta a eventi a livello di sistema o Intent. Tuttavia, i ricevitori di trasmissioni non forniscono alcun controllo sul momento in cui il processo deve essere eseguito. Inoltre, le modifiche apportate al sistema operativo Android verranno limitate quando i ricevitori di trasmissioni funzioneranno o i tipi di lavoro a cui possono rispondere. 

Sono disponibili due funzionalità principali per eseguire in modo efficiente il lavoro in background (talvolta definito _processo in background_ o _processo_):

1. **Pianificazione intelligente del lavoro** &ndash; È importante che, quando un'applicazione esegue il lavoro in background, in quanto è un valido cittadino. Idealmente, l'applicazione non deve richiedere l'esecuzione di un processo. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per il momento in cui il processo può essere eseguito, quindi pianificare il lavoro da eseguire quando vengono soddisfatte le condizioni. Ciò consente ad Android di eseguire in modo intelligente il lavoro. Ad esempio, è possibile che le richieste di rete vengano eseguite in batch in modo che vengano eseguite contemporaneamente per sfruttare al massimo l'overhead richiesto per la rete.
2. **Incapsulamento del lavoro** &ndash; Il codice per eseguire le operazioni in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sarà relativamente semplice da ripianificare se il lavoro non viene completato per qualche motivo.

Il dispatcher del processo Firebase è una libreria di Google che fornisce un'API Fluent per semplificare la pianificazione del lavoro in background. È destinato a essere la sostituzione di Google Cloud Manager. Il dispatcher del processo Firebase è costituito dalle API seguenti:

- Un oggetto `Firebase.JobDispatcher.JobService` è una classe astratta che deve essere estesa con la logica che verrà eseguita nel processo in background.
- Un oggetto `Firebase.JobDispatcher.JobTrigger` dichiara quando il processo deve essere avviato. Questa operazione viene in genere espressa come una finestra temporale, ad esempio, attendere almeno 30 secondi prima di avviare il processo, ma eseguire il processo entro 5 minuti.
- Un oggetto `Firebase.JobDispatcher.RetryStrategy` contiene informazioni sulle operazioni che devono essere eseguite quando un processo non viene eseguito correttamente. La strategia di ripetizione dei tentativi specifica il tempo di attesa prima di tentare di eseguire nuovamente il processo. 
- Un `Firebase.JobDispatcher.Constraint` è un valore facoltativo che descrive una condizione che deve essere soddisfatta prima che il processo possa essere eseguito, ad esempio se il dispositivo si trova in una rete non a consumo o si sta caricando.
- `Firebase.JobDispatcher.Job`È un'API che unifica le API precedenti in a un'unità di lavoro che può essere pianificata da `JobDispatcher` . La `Job.Builder` classe viene utilizzata per creare un'istanza di un oggetto `Job` .
- Un `Firebase.JobDispatcher.JobDispatcher` Usa le tre API precedenti per pianificare il lavoro con il sistema operativo e per fornire un modo per annullare i processi, se necessario.

Per pianificare il lavoro con il dispatcher del processo Firebase, un'applicazione Novell. Android deve incapsulare il codice in un tipo che estende la `JobService` classe. `JobService`dispone di tre metodi del ciclo di vita che possono essere chiamati durante il processo:

- **`bool OnStartJob(IJobParameters parameters)`**&ndash;Questo metodo è il punto in cui si verificherà il lavoro e deve essere sempre implementato. Viene eseguito nel thread principale. Questo metodo restituirà `true` se il lavoro rimane o `false` se il lavoro viene eseguito. 
- **`bool OnStopJob(IJobParameters parameters)`**&ndash;Questo metodo viene chiamato quando il processo viene arrestato per qualche motivo. Deve restituire `true` se il processo deve essere ripianificato per un momento successivo.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`**&ndash;Questo metodo viene chiamato quando l'oggetto `JobService` ha terminato un lavoro asincrono. 

Per pianificare un processo, l'applicazione creerà un'istanza di un `JobDispatcher` oggetto. `Job.Builder`Viene quindi usato un oggetto per creare un `Job` oggetto, fornito a che tenterà di `JobDispatcher` pianificare l'esecuzione del processo.

Questa guida illustra come aggiungere il dispatcher del processo di Firebase a un'applicazione Novell. Android e usarlo per pianificare il lavoro in background.

## <a name="requirements"></a>Requisiti

Il dispatcher del processo Firebase richiede il livello 9 dell'API Android o versione successiva. La libreria del dispatcher del processo Firebase si basa su alcuni componenti forniti da Google Play Services; nel dispositivo deve essere installato Google Play Services.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso della libreria del dispatcher di processi Firebase in Novell. Android

Per iniziare a usare il dispatcher del processo Firebase, aggiungere prima il [pacchetto NuGet Novell. Firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al progetto Novell. Android. Eseguire una ricerca in Gestione pacchetti NuGet per il pacchetto **Novell. Firebase. JobDispatcher** (che è ancora in versione non definitiva).

Dopo aver aggiunto la libreria del dispatcher dei processi Firebase, creare una `JobService` classe e quindi pianificarne l'esecuzione con un'istanza del `FirebaseJobDispatcher` .

### <a name="creating-a-jobservice"></a>Creazione di un JobService

Tutte le operazioni eseguite dalla libreria del dispatcher dei processi Firebase devono essere eseguite in un tipo che estende la `Firebase.JobDispatcher.JobService` classe astratta. La creazione di un oggetto `JobService` è molto simile alla creazione di una `Service` con il Framework Android: 

1. Estendi la `JobService` classe
2. Decorare la sottoclasse con `ServiceAttribute` . Sebbene non sia strettamente obbligatorio, è consigliabile impostare in modo esplicito il `Name` parametro per facilitare il debug di `JobService` . 
3. Aggiungere un oggetto `IntentFilter` per dichiarare `JobService` in **file AndroidManifest. XML**. Questa operazione aiuterà inoltre la libreria del dispatcher del processo Firebase a individuare e richiamare `JobService` .

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

Prima di poter pianificare qualsiasi lavoro, è necessario creare un `Firebase.JobDispatcher.FirebaseJobDispatcher` oggetto. `FirebaseJobDispatcher`È responsabile della pianificazione di un oggetto `JobService` . Il frammento di codice seguente è un modo per creare un'istanza di `FirebaseJobDispatcher` : 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Nel frammento di codice precedente, la `GooglePlayDriver` classe è che consente `FirebaseJobDispatcher` di interagire con alcune delle API di pianificazione in Google Play Services sul dispositivo. Il parametro `context` è qualsiasi Android `Context` , ad esempio un'attività. Attualmente `GooglePlayDriver` è l'unica `IDriver` implementazione nella libreria del dispatcher del processo Firebase. 

Il binding Novell. Android per il dispatcher del processo Firebase fornisce un metodo di estensione per creare un oggetto `FirebaseJobDispatcher` da `Context` : 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una volta `FirebaseJobDispatcher` creata un'istanza di, è possibile creare un oggetto `Job` ed eseguire il codice nella `JobService` classe. `Job`Viene creato da un `Job.Builder` oggetto e verrà illustrato nella sezione successiva.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creazione di un Firebase. JobDispatcher. job con Job. Builder

La `Firebase.JobDispatcher.Job` classe è responsabile dell'incapsulamento dei metadati necessari per eseguire `JobService` . Un oggetto `Job` contiene informazioni quali tutti i vincoli che devono essere soddisfatti prima di poter eseguire il processo, se `Job` è ricorrente o qualsiasi trigger che provocherà l'esecuzione del processo.  Come minimo, un `Job` deve avere un _tag_ (una stringa univoca che identifica il processo a `FirebaseJobDispatcher` ) e il tipo dell'oggetto `JobService` che deve essere eseguito. Il dispatcher del processo Firebase creerà un'istanza `JobService` di quando è il momento di eseguire il processo.  `Job`Viene creato un oggetto utilizzando un'istanza della `Firebase.JobDispatcher.Job.JobBuilder` classe. 

Il frammento di codice seguente è l'esempio più semplice per creare un `Job` usando il binding Novell. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`In vengono eseguiti alcuni controlli di convalida di base sui valori di input per il processo. Se non è possibile per la creazione di un oggetto, verrà generata un'eccezione `Job.Builder` `Job` .  Creerà `Job.Builder` un oggetto `Job` con i valori predefiniti seguenti:

- La `Job` _durata_ di un periodo di tempo (il tempo pianificato per l'esecuzione) è solo fino al riavvio del dispositivo dopo il riavvio del dispositivo &ndash; `Job` .
- Un oggetto `Job` non è ricorrente &ndash; e verrà eseguito una sola volta.
- Un `Job` verrà pianificato per l'esecuzione appena possibile.
- La strategia di ripetizione dei tentativi predefinita per a `Job` prevede l'uso di un _backoff esponenziale_ (illustrato più dettagliatamente nella sezione [impostazione di un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Dopo la creazione di `Job` , è necessario pianificarlo con `FirebaseJobDispatcher` prima che venga eseguito. Esistono due metodi per pianificare un oggetto `Job` :

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

Il valore restituito da `FirebaseJobDispatcher.Schedule` sarà uno dei valori interi seguenti:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash;La `Job` pianificazione di è stata completata.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`&ndash;Si è verificato un problema sconosciuto che ha impedito la `Job` pianificazione di.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`&ndash;È stato usato un oggetto non valido `IDriver` o `IDriver` è stato non disponibile. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash;L'oggetto `Trigger` non è supportato.
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash;Il servizio non è configurato correttamente o non è disponibile.

### <a name="configuring-a-job"></a>Configurazione di un processo

È possibile personalizzare un processo. Di seguito sono riportati alcuni esempi di come è possibile personalizzare un processo:

- [Passaggio di parametri a un processo](#Passing_Parameters_to_a_Job) &ndash; Un `Job` potrebbe richiedere valori aggiuntivi per eseguire il proprio lavoro, ad esempio il download di un file.
- [Imposta vincoli](#Setting_Constraints) &ndash; Potrebbe essere necessario eseguire un processo solo quando vengono soddisfatte determinate condizioni. Ad esempio, eseguire solo un `Job` quando il dispositivo viene caricato. 
- [Specificare quando `Job` eseguire](#Setting_Job_Triggers) &ndash; un'operazione Il dispatcher del processo Firebase consente alle applicazioni di specificare l'ora in cui deve essere eseguito il processo.  
- [Dichiarare una strategia di ripetizione dei tentativi per i processi](#Setting_a_RetryStrategy) &ndash; non riusciti Una _strategia di ripetizione dei tentativi_ fornisce indicazioni per le `FirebaseJobDispatcher` operazioni da eseguire in caso di `Jobs` esito negativo. 

Ognuno di questi argomenti verrà illustrato in dettaglio nelle sezioni seguenti.

<a name="Passing_Parameters_to_a_Job"></a>

#### <a name="passing-parameters-to-a-job"></a>Passaggio di parametri a un processo

I parametri vengono passati a un processo creando un oggetto `Bundle` che viene passato insieme al `Job.Builder.SetExtras` Metodo:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle`È possibile accedere alla `IJobParameters.Extras` proprietà nel `OnStartJob` Metodo:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints"></a>

#### <a name="setting-constraints"></a>Impostazione di vincoli

I vincoli consentono di ridurre i costi o lo svuotamento della batteria sul dispositivo. La `Firebase.JobDispatcher.Constraint` classe definisce questi vincoli come valori integer:

- `Constraint.OnUnmeteredNetwork`&ndash;Eseguire il processo solo quando il dispositivo è connesso a una rete non a consumo. Questa operazione è utile per impedire all'utente di incorrere in addebiti per i dati.
- `Constraint.OnAnyNetwork`&ndash;Eseguire il processo in qualsiasi rete a cui è connesso il dispositivo. Se viene specificato insieme a `Constraint.OnUnmeteredNetwork` , questo valore ha la priorità.
- `Constraint.DeviceCharging`&ndash;Eseguire il processo solo quando il dispositivo viene caricato.

I vincoli vengono impostati con il `Job.Builder.SetConstraint` Metodo: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers"></a>

`JobTrigger`Fornisce indicazioni al sistema operativo in merito all'avvio del processo. Un oggetto dispone di una `JobTrigger` _finestra in esecuzione_ che definisce un'ora pianificata per l' `Job` esecuzione di. La finestra di esecuzione ha un valore della finestra _iniziale_ e un valore della _finestra finale_ . La finestra iniziale indica il numero di secondi di attesa del dispositivo prima dell'esecuzione del processo e il valore della finestra finale è il numero massimo di secondi di attesa prima dell'esecuzione di `Job` . 

Un oggetto `JobTrigger` può essere creato con il `Firebase.Jobdispatcher.Trigger.ExecutionWindow` metodo.  Ad esempio, `Trigger.ExecutionWindow(15,60)` il processo deve essere eseguito tra 15 e 60 secondi da quando è pianificato. Il `Job.Builder.SetTrigger` metodo viene usato per 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

L'impostazione predefinita `JobTrigger` per un processo è rappresentata dal valore `Trigger.Now` , che specifica che un processo deve essere eseguito il prima possibile dopo essere stato pianificato.

<a name="Setting_a_RetryStrategy"></a>

#### <a name="setting-a-retrystrategy"></a>Impostazione di un RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`Viene usato per specificare la quantità di ritardo che un dispositivo deve usare prima di provare a eseguire nuovamente un processo non riuscito. Un oggetto `RetryStrategy` dispone di un _criterio_che definisce l'algoritmo di base temporale che verrà utilizzato per ripianificare il processo non riuscito e una finestra di esecuzione che specifica una finestra in cui il processo deve essere pianificato. Questa _finestra di ripianificazione_ è definita da due valori. Il primo valore è il numero di secondi di attesa prima di ripianificare il processo (il valore _iniziale di backoff_ ) e il secondo numero è il numero massimo di secondi prima che il processo venga eseguito (il valore _backoff massimo_ ). 

I due tipi di criteri di ripetizione sono identificati da questi valori int:

- `RetryStrategy.RetryPolicyExponential`&ndash;Un criterio _backoff esponenziale_ aumenterà in modo esponenziale il valore iniziale di backoff dopo ogni errore. La prima volta che si verifica un errore di un processo, la libreria attenderà l'intervallo di _initial specificato prima di ripianificare l'esempio di processo &ndash; 30 secondi. La seconda volta che il processo non riesce, la libreria attenderà almeno 60 secondi prima di provare a eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria attenderà 120 secondi e così via. Il valore predefinito `RetryStrategy` per la libreria del dispatcher del processo Firebase è rappresentato dall' `RetryStrategy.DefaultExponential` oggetto. Ha una backoff iniziale di 30 secondi e un backoff massimo di 3600 secondi.
- `RetryStrategy.RetryPolicyLinear`&ndash;Questa strategia è un _backoff lineare_ che il processo deve essere ripianificato per l'esecuzione a intervalli prestabiliti (fino a quando non riesce). Il backoff lineare è più adatto per il lavoro che deve essere completato il prima possibile o per i problemi che si risolveranno rapidamente. La libreria del dispatcher del processo Firebase definisce un `RetryStrategy.DefaultLinear` che ha una finestra di ripianificazione di almeno 30 secondi e fino a 3600 secondi.

È possibile definire un oggetto personalizzato `RetryStrategy` con il `FirebaseJobDispatcher.NewRetryStrategy` metodo. Sono necessari tre parametri:

1. `int policy`&ndash;Il _criterio_ è uno dei valori precedenti `RetryStrategy` , `RetryStrategy.RetryPolicyLinear` o `RetryStrategy.RetryPolicyExponential` .
2. `int initialBackoffSeconds`&ndash;Il valore _iniziale di backoff_ è un ritardo, in secondi, necessario prima di ritentare l'esecuzione del processo. Il valore predefinito è 30 secondi. 
3. `int maximumBackoffSeconds`&ndash;Il valore _massimo di backoff_ dichiara il numero massimo di secondi di ritardo prima di provare a eseguire nuovamente il processo. Il valore predefinito è 3600 secondi. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Annullamento di un processo

È possibile annullare tutti i processi pianificati o solo un singolo processo usando il `FirebaseJobDispatcher.CancelAll()` metodo o il `FirebaseJobDispatcher.Cancel(string)` Metodo:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Entrambi i metodi restituiranno un valore integer:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash;Il processo è stato annullato.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash;Un errore ha impedito l'annullamento del processo.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`&ndash;Non `FirebaseJobDispatcher` è in grado di annullare il processo perché non è disponibile alcun valido `IDriver` .

## <a name="summary"></a>Summary

Questa guida ha illustrato come usare il dispatcher del processo Firebase per eseguire in modo intelligente il lavoro in background. Viene illustrato come incapsulare il lavoro da eseguire come `JobService` e come utilizzare `FirebaseJobDispatcher` per pianificare il lavoro, specificando i criteri con un oggetto e la modalità di gestione degli `JobTrigger` errori con un oggetto `RetryStrategy` .

## <a name="related-links"></a>Collegamenti correlati

- [Novell. Firebase. JobDispatcher in NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [Firebase-job-Dispatcher su GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Binding Novell. Firebase. JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Pianificazione intelligente del processo](https://developer.android.com/topic/performance/scheduling.html)
- [Ottimizzazioni della memoria e della batteria Android-Google I/O 2016 (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
