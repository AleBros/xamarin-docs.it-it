---
title: Firebase Job Dispatcher
description: Questa guida illustra come pianificare il lavoro in background utilizzando la libreria Firebase Job Dispatcher di Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020241"
---
# <a name="firebase-job-dispatcher"></a>Firebase Job Dispatcher

_Questa guida illustra come pianificare il lavoro in background utilizzando la libreria Firebase Job Dispatcher di Google._

## <a name="overview"></a>Panoramica

Uno dei modi migliori per mantenere un'applicazione Android reattiva all'utente consiste nel garantire che il lavoro complesso o a esecuzione prolungata venga eseguito in background. Tuttavia, è importante che il lavoro in background non influisca negativamente sull'esperienza dell'utente con il dispositivo. 

Ad esempio, un processo in background potrebbe eseguire il polling di un sito Web ogni tre o quattro minuti per eseguire una query per le modifiche a un determinato set di dati. Questo sembra benigno, tuttavia avrebbe un impatto disastroso sulla durata della batteria. L'applicazione riattiverà ripetutamente il dispositivo, eleverà la CPU a uno stato di alimentazione superiore, accenderà le radio, effettuerà le richieste di rete e quindi elaborazione dei risultati. Peggiora perché il dispositivo non si spegne immediatamente e ritorna allo stato di inattività a basso consumo. Il lavoro in background non programmato correttamente può inavvertitamente mantenere il dispositivo in uno stato con requisiti di alimentazione inutili ed eccessivi. Questa attività apparentemente innocente (polling di un sito web) renderà il dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le seguenti API per facilitare l'esecuzione di lavoro in background, ma da soli non sono sufficienti per la pianificazione intelligente dei processi. 

- **[I servizi intento](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; di intenti sono ideali per l'esecuzione del lavoro, tuttavia non forniscono alcun modo per pianificare il lavoro.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Queste API consentono solo di pianificare il lavoro, ma non forniscono alcun modo per eseguire effettivamente il lavoro. Inoltre, il AlarmManager consente solo vincoli basati sul tempo, il che significa generare un allarme in un determinato momento o dopo un determinato periodo di tempo è trascorso. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule JobSchedule è un'API di grandi qualità che funziona con il sistema operativo per pianificare i processi. Tuttavia, è disponibile solo per le app Android destinate al livello API 21 o superiore. 
- **[Ricevitori](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; di trasmissione Un'app Android può configurare ricevitori di trasmissione per eseguire operazioni in risposta a eventi o finalità a livello di sistema. Tuttavia, i ricevitori broadcast non forniscono alcun controllo su quando il processo deve essere eseguito. Anche le modifiche nel sistema operativo Android limiteranno quando i ricevitori di trasmissione funzioneranno, o il tipo di lavoro a cui possono rispondere. 

Esistono due caratteristiche chiave per eseguire in modo efficiente il lavoro in background (talvolta indicato come un lavoro in _background_ o un _lavoro_):

1. **Pianificazione intelligente del lavoro** &ndash; È importante che quando un'applicazione sta facendo il lavoro in background che lo fa come un buon cittadino. In teoria, l'applicazione non deve richiedere l'esecuzione di un processo. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per quando il processo può essere eseguito e quindi pianificare il lavoro da eseguire quando vengono soddisfatte le condizioni. Questo permette Android per eseguire in modo intelligente il lavoro. Ad esempio, le richieste di rete possono essere raggruppate in batch per l'esecuzione contemporanea per sfruttare al massimo l'overhead associato alla rete.
2. **Incapsulamento del lavoro** &ndash; Il codice per eseguire il lavoro in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sarà relativamente facile da ripianificare se il lavoro non viene completato per qualche motivo.

Firebase Job Dispatcher è una libreria di Google che fornisce un'API fluente per semplificare la pianificazione del lavoro in background. È destinato a sostituire Google Cloud Manager. Firebase Job Dispatcher è costituito dalle seguenti API:

- A `Firebase.JobDispatcher.JobService` è una classe astratta che deve essere estesa con la logica che verrà eseguita nel processo in background.
- Un `Firebase.JobDispatcher.JobTrigger` dichiara quando il processo deve essere avviato. Si tratta in genere espresso come un intervallo di tempo, ad esempio, attendere almeno 30 secondi prima di avviare il processo, ma eseguire il processo entro 5 minuti.
- A `Firebase.JobDispatcher.RetryStrategy` contiene informazioni sulle operazioni da eseguire quando un processo non viene eseguito correttamente. La strategia di ripetizione dei tentativi specifica il tempo di attesa prima di tentare di eseguire nuovamente il processo. 
- A `Firebase.JobDispatcher.Constraint` è un valore facoltativo che descrive una condizione che deve essere soddisfatta prima che il processo possa essere eseguito, ad esempio il dispositivo si trova in una rete senza parametri o in carica.
- Si `Firebase.JobDispatcher.Job` tratta di un'API che unifica le API precedenti in un'unità `JobDispatcher`di lavoro che può essere pianificata dal file . La `Job.Builder` classe viene utilizzata `Job`per creare un'istanza di .
- A `Firebase.JobDispatcher.JobDispatcher` utilizza le tre API precedenti per pianificare il lavoro con il sistema operativo e per fornire un modo per annullare i processi, se necessario.

Per pianificare il lavoro con Firebase Job Dispatcher, un'applicazione Xamarin.Android deve incapsulare il codice in un tipo che estende la `JobService` classe. `JobService`dispone di tre metodi del ciclo di vita che possono essere chiamati durante la durata del processo:

- **`bool OnStartJob(IJobParameters parameters)`**&ndash; Questo metodo è dove si verificherà il lavoro e deve sempre essere implementato. Viene eseguito sul thread principale. Questo metodo `true` restituirà se c'è lavoro rimanente, o `false` se il lavoro è fatto. 
- **`bool OnStopJob(IJobParameters parameters)`**&ndash; Viene chiamato quando il processo viene arrestato per qualche motivo. Dovrebbe restituire `true` se il lavoro deve essere riprogrammato per un secondo momento.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`**&ndash; Questo metodo viene `JobService` chiamato quando l'oggetto ha terminato qualsiasi lavoro asincrono. 

Per pianificare un processo, `JobDispatcher` l'applicazione creerà un'istanza di un oggetto. Quindi, `Job.Builder` viene utilizzato un `Job` oggetto, che viene `JobDispatcher` fornito al che tenterà di pianificare l'esecuzione del processo.

Questa guida verrà illustrato come aggiungere il Dispatcher di lavoro Firebase a un'applicazione Xamarin.Android e utilizzarlo per pianificare il lavoro in background.

## <a name="requirements"></a>Requisiti

Firebase Job Dispatcher richiede il livello API Android 9 o superiore. La libreria Firebase Job Dispatcher si basa su alcuni componenti forniti da Google Play Services; nel dispositivo deve essere installato Google Play Services.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Utilizzo della libreria Firebase Job Dispatcher in Xamarin.Android

Per iniziare con Firebase Job Dispatcher, aggiungere innanzitutto il [pacchetto Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al progetto Xamarin.Android. Cercare il pacchetto **Xamarin.Firebase.JobDispatcher** in Gestione pacchetti NuGet (che è ancora in versione non definitiva).

Dopo aver aggiunto la libreria Firebase Job Dispatcher, creare una `JobService` classe `FirebaseJobDispatcher`e pianificarne l'esecuzione con un'istanza di .

### <a name="creating-a-jobservice"></a>Creazione di un JobServiceCreating a JobService

Tutto il lavoro eseguito dalla libreria Firebase Job Dispatcher `Firebase.JobDispatcher.JobService` deve essere eseguito in un tipo che estende la classe astratta. Creazione `JobService` di un è `Service` molto simile alla creazione di un con il framework Android:Creating a is very similar to creating a with the Android framework: 

1. Estendi la `JobService` classe
2. Decorare la sottoclasse `ServiceAttribute`con il file . Sebbene non sia strettamente necessario, `Name` si consiglia di `JobService`impostare in modo esplicito il parametro per facilitare il debug di . 
3. Aggiungere `IntentFilter` un per `JobService` dichiarare il nel file **AndroidManifest.xml**. Ciò consentirà inoltre alla libreria Firebase `JobService`Job Dispatcher di individuare e richiamare il file .

Il codice seguente è un `JobService` esempio del più semplice per un'applicazione, utilizzando la proprietà TPL per eseguire alcune operazioni in modo asincrono:The following code is an example of the simplest for an application, using the TPL to asynchronously perform some work:

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

### <a name="creating-a-firebasejobdispatcher"></a>Creazione di un FirebaseJobDispatcherCreating a FirebaseJobDispatcher

Prima di poter programmare qualsiasi lavoro, `Firebase.JobDispatcher.FirebaseJobDispatcher` è necessario creare un oggetto. L'oggetto `FirebaseJobDispatcher` è responsabile della programmazione di un `JobService`file . Il frammento di codice seguente consente `FirebaseJobDispatcher`di creare un'istanza di : 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Nel frammento di `GooglePlayDriver` codice precedente, `FirebaseJobDispatcher` la classe is che consente di interagire con alcune delle API di pianificazione in Google Play Services sul dispositivo. Il `context` parametro `Context`è qualsiasi Android , ad esempio un'attività. Attualmente `GooglePlayDriver` è l'unica `IDriver` implementazione nella libreria Firebase Job Dispatcher. 

L'associazione Xamarin.Android per Firebase Job Dispatcher `FirebaseJobDispatcher` fornisce `Context`un metodo di estensione per creare un dal : 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una `FirebaseJobDispatcher` volta creata un'istanza di , `Job` è possibile creare `JobService` un ed eseguire il codice nella classe. L'oggetto `Job` viene `Job.Builder` creato da un oggetto e verrà discusso nella sezione successiva.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creazione di un Firebase.JobDispatcher.Job con Job.Builder

La `Firebase.JobDispatcher.Job` classe è responsabile dell'incapsulamento dei `JobService`metadati necessari per eseguire un oggetto . Un`Job` contiene informazioni quali qualsiasi vincolo che deve essere `Job` soddisfatto prima che il processo possa essere eseguito, se l'oggetto è ricorrente o qualsiasi trigger che causerà l'esecuzione del processo.  Come minimo indispensabile, `Job` un elemento deve avere un _tag_ (una stringa univoca che identifica il processo per il `FirebaseJobDispatcher`) e il tipo di `JobService` che deve essere eseguito. Firebase Job Dispatcher creerà un'istanza di `JobService` quando è il momento di eseguire il processo.  Un `Job` oggetto viene creato utilizzando `Firebase.JobDispatcher.Job.JobBuilder` un'istanza della classe. 

Il frammento di codice seguente è `Job` l'esempio più semplice di come creare un utilizzando l'associazione Xamarin.Android:The following code snippet is the simplest example of how to create a using the Xamarin.Android binding:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

L'oggetto `Job.Builder` eseguirà alcuni controlli di convalida di base sui valori di input per il processo. Se non è possibile `Job.Builder` creare un `Job`oggetto .  Verrà `Job.Builder` creato `Job` un con le seguenti impostazioni predefinite:

- La `Job` _durata_ (per quanto tempo verrà pianificata per l'esecuzione) è solo fino al riavvio del dispositivo &ndash; dopo il riavvio `Job` del dispositivo.
- Un `Job` oggetto &ndash; non è ricorrente verrà eseguito una sola volta.
- A `Job` verrà pianificato per l'esecuzione il prima possibile.
- La strategia di `Job` ripetizione dei tentativi predefinita per un oggetto consiste nell'utilizzare un _backoff esponenziale_ (illustrato più dettagliatamente di seguito nella sezione Impostazione di [un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Dopo aver `Job`creato l'oggetto , `FirebaseJobDispatcher` è necessario pianificarlo con l'operazione prima dell'esecuzione. Esistono due metodi per `Job`programmare un:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

Il valore restituito `FirebaseJobDispatcher.Schedule` da sarà uno dei seguenti valori interi:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; L'operazione `Job` è stata pianificata con successo.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`&ndash; Si è verificato un `Job` problema sconosciuto che ha impedito la pianificazione.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`&ndash; È `IDriver` stato utilizzato `IDriver` un invalido o l'era in qualche modo non disponibile. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash; Il `Trigger` non è stato supportato.
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash; Il servizio non è configurato correttamente o non è disponibile.

### <a name="configuring-a-job"></a>Configurazione di un processo

È possibile personalizzare un lavoro. Di seguito sono riportati alcuni esempi di personalizzazione di un processo:

- [Il passaggio di parametri a un processo](#Passing_Parameters_to_a_Job) &ndash; A `Job` può richiedere valori aggiuntivi per eseguire il proprio lavoro, ad esempio il download di un file.
- [Imposta vincoli](#Setting_Constraints) &ndash; Potrebbe essere necessario eseguire un processo solo quando vengono soddisfatte determinate condizioni. Ad esempio, eseguire `Job` un solo quando il dispositivo è in carica. 
- [Specificare `Job` quando un dispatcher](#Setting_Job_Triggers) &ndash; di processo Firebase consente alle applicazioni di specificare un'ora in cui il processo deve essere eseguito.  
- [Dichiarare una strategia di ripetizione dei tentativi per i processi non riusciti](#Setting_a_RetryStrategy) &ndash; Una strategia di _ripetizione dei tentativi_ fornisce indicazioni sulle `FirebaseJobDispatcher` operazioni da eseguire per `Jobs` il completamento. 

Ognuno di questi argomenti verrà discusso più nelle sezioni seguenti.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passaggio di parametri a un processoPassing parameters to a job

I parametri vengono passati `Bundle` a un processo `Job.Builder.SetExtras` creando un oggetto che viene passato insieme al metodo :

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

È `Bundle` possibile accedere `IJobParameters.Extras` alla `OnStartJob` proprietà dal metodo :

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Impostazione dei vincoli

I vincoli consentono di ridurre i costi o lo scarico della batteria sul dispositivo. La `Firebase.JobDispatcher.Constraint` classe definisce questi vincoli come valori integer:The class defines these constraints as integer values:

- `Constraint.OnUnmeteredNetwork`&ndash; Eseguire il processo solo quando il dispositivo è connesso a una rete senza tassametri. Ciò è utile per impedire all'utente di incorrere in addebiti per i dati.
- `Constraint.OnAnyNetwork`&ndash; Eseguire il processo su qualsiasi rete a cui è connesso il dispositivo. Se specificato `Constraint.OnUnmeteredNetwork`insieme a , questo valore avrà la priorità.
- `Constraint.DeviceCharging`&ndash; Eseguire il processo solo quando il dispositivo è in carica.

I vincoli `Job.Builder.SetConstraint` vengono impostati con il metodo : 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

Il `JobTrigger` fornisce indicazioni al sistema operativo su quando il processo deve essere avviato. A `JobTrigger` dispone di una _finestra di esecuzione_ che `Job` definisce un'ora pianificata per il momento in cui l'esecuzione deve essere eseguita. La finestra di esecuzione ha un valore di _finestra iniziale_ e un valore di _finestra finale._ La finestra di avvio è il numero di secondi che il dispositivo deve attendere prima di `Job`eseguire il processo e il valore della finestra finale è il numero massimo di secondi di attesa prima di eseguire il file . 

Oggetto `JobTrigger` può essere `Firebase.Jobdispatcher.Trigger.ExecutionWindow` creato con il metodo .  Ad `Trigger.ExecutionWindow(15,60)` esempio, significa che il processo deve essere eseguito tra 15 e 60 secondi da quando è pianificato. Il `Job.Builder.SetTrigger` metodo viene utilizzato per 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

L'impostazione predefinita `JobTrigger` per un `Trigger.Now`processo è rappresentata dal valore , che specifica che un processo deve essere eseguito il prima possibile dopo essere stato pianificato.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Impostazione di un RetryStrategySetting a RetryStrategy

L'oggetto `Firebase.JobDispatcher.RetryStrategy` viene utilizzato per specificare la quantità di ritardo che un dispositivo deve utilizzare prima di tentare di eseguire nuovamente un processo non riuscito. Un `RetryStrategy` _criterio_, che definisce l'algoritmo di base temporale che verrà utilizzato per ripianificare il processo non riuscito e una finestra di esecuzione che specifica una finestra in cui il processo deve essere pianificato. Questa _finestra di riprogrammazione_ è definita da due valori. Il primo valore è il numero di secondi di attesa prima di ripianificare il processo (il valore _di backoff iniziale)_ e il secondo numero è il numero massimo di secondi prima che il processo deve essere eseguito (il valore _di backoff massimo)._ 

I due tipi di criteri di ripetizione dei tentativi sono identificati da questi valori int:

- `RetryStrategy.RetryPolicyExponential`&ndash; Un criterio _di backoff esponenziale_ aumenterà il valore di backoff iniziale in modo esponenziale dopo ogni errore. La prima volta che un processo ha esito negativo, la libreria &ndash; attenderà l'intervallo di _initial specificato prima di ripianificare l'esempio di processo 30 secondi. La seconda volta che il processo ha esito negativo, la libreria attenderà almeno 60 secondi prima di tentare di eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria attenderà 120 secondi e così via. Il `RetryStrategy` valore predefinito per la libreria Firebase Job Dispatcher è rappresentato dall'oggetto. `RetryStrategy.DefaultExponential` Ha un backoff iniziale di 30 secondi e un backoff massimo di 3600 secondi.
- `RetryStrategy.RetryPolicyLinear`&ndash; Questa strategia è un _backoff lineare_ che il processo deve essere riprogrammato per l'esecuzione a intervalli prestabiliti (fino a quando non riesce). Il backoff lineare è più adatto per lavori che devono essere completati il prima possibile o per problemi che si risolveranno rapidamente. La libreria Firebase Job `RetryStrategy.DefaultLinear` Dispatcher definisce un che ha una finestra di riprogrammazione di almeno 30 secondi e fino a 3600 secondi.

È possibile definire un'usanza `RetryStrategy` con il `FirebaseJobDispatcher.NewRetryStrategy` metodo. Accetta tre parametri:

1. `int policy`&ndash; Il _criterio_ è uno `RetryStrategy` dei `RetryStrategy.RetryPolicyLinear`valori `RetryStrategy.RetryPolicyExponential`precedenti, , o .
2. `int initialBackoffSeconds`&ndash; Il _backoff iniziale_ è un ritardo, in secondi, necessario prima di provare a eseguire nuovamente il processo. Il valore predefinito è 30 secondi. 
3. `int maximumBackoffSeconds`&ndash; Il valore _di backoff massimo_ dichiara il numero massimo di secondi di ritardo prima di tentare di eseguire nuovamente il processo. Il valore predefinito è 3600 secondi. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Annullamento di un processo

È possibile annullare tutti i processi pianificati o solo `FirebaseJobDispatcher.CancelAll()` un singolo `FirebaseJobDispatcher.Cancel(string)` processo utilizzando il metodo o il metodo:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Entrambi i metodi restituiranno un valore intero:Either method will return an integer value:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash; Il lavoro è stato annullato con successo.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash; Un errore ha impedito l'annullamento del processo.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`&ndash; Impossibile `FirebaseJobDispatcher` annullare il processo perché `IDriver` non è disponibile alcun acquisinotta.

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come utilizzare Firebase Job Dispatcher per eseguire in modo intelligente il lavoro in background. Viene illustrato come incapsulare il `JobService` lavoro da eseguire `FirebaseJobDispatcher` come un e come utilizzare `JobTrigger` il per pianificare tale lavoro, specificando i criteri con un e come gli errori devono essere gestiti con un `RetryStrategy`oggetto .

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Firebase.JobDispatcher su NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-job-dispatcher su GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Associazione Xamarin.Firebase.JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Pianificazione intelligente dei processi](https://developer.android.com/topic/performance/scheduling.html)
- [Ottimizzazioni della batteria e della memoria Android - Google I/O 2016 (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
