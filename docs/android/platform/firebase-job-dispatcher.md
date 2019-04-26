---
title: Firebase Job Dispatcher
description: Questa guida illustra come pianificare l'attività in background usando la libreria di Firebase processo Dispatcher da Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 91bafbbdaee805ad128766bf0a770cb711597a85
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023357"
---
# <a name="firebase-job-dispatcher"></a>Firebase Job Dispatcher

_Questa guida illustra come pianificare l'attività in background usando la libreria di Firebase processo Dispatcher da Google._


## <a name="overview"></a>Panoramica

Uno dei modi migliori per mantenere reattiva verso utente un'applicazione Android è garantire che il lavoro lunga o complesso viene eseguito in background. Tuttavia, è importante che attività in background non influiranno negativamente sull'esperienza dell'utente con il dispositivo. 

Ad esempio, un processo in background potrebbe eseguire il polling di un sito Web ogni tre o quattro minuti per eseguire query per le modifiche a un determinato set di dati. Ciò sembra non dannoso, ma avrebbe un impatto disastroso sulla durata della batteria. L'applicazione verrà ripetutamente riattivare il dispositivo, elevare la CPU a uno stato di alimentazione superiore, accendere la radio, apportare le richieste di rete e quindi elaborando i risultati. Ottiene il peggiore perché il dispositivo verrà immediatamente non spegnere e tornare allo stato inattivo basso consumo. Attività in background in modo inadeguato pianificata potrebbe inavvertitamente tenere il dispositivo in stato di alimentazione superflua ed eccessiva. Questa attività apparentemente innocua (polling di un sito Web) sarà inutilizzabile il dispositivo in un periodo di tempo relativamente breve.

Android fornisce le API seguenti per facilitare l'esecuzione di lavoro in background, ma da soli non sono sufficienti per la pianificazione dei processi intelligenti. 

* **[Servizi Intent](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; con finalità di servizi sono ideali per l'esecuzione del lavoro, tuttavia non forniscono alcuna possibilità di pianificare il lavoro.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; queste API consentono solo lavoro pianificata, ma non consentono di eseguire effettivamente il lavoro. Inoltre, il AlarmManager consente solo vincoli basato sul tempo, ovvero a generare un avviso a una determinata ora o dopo che è trascorso un determinato periodo di tempo. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule The è un'ottima API che funziona con il sistema operativo per pianificare i processi. Tuttavia, è solo disponibile per le app Android destinate a livello API 21 o versione successiva. 
* **[Ricevitori di trasmissione](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; An Android app è possibile configurare ricevitori di trasmissioni per eseguire le operazioni in risposta a eventi a livello di sistema o Intent. Ricevitori di trasmissioni non forniscono tuttavia controllare quando il processo deve essere eseguito. Limitano inoltre le modifiche nel sistema operativo Android quando ricevitori di trasmissioni funzioneranno o i tipi di lavoro che rispondano alle. 

Esistono due funzionalità fondamentali per l'esecuzione in modo efficiente attività in background (talvolta detta un _processo in background_ o una _processo_):

1. **Le operazioni di pianificazione in modo intelligente** &ndash; è importante che quando un'applicazione esegue operazioni in background che esegue l'operazione come un elemento positivo. In teoria, l'applicazione non deve richiedere che un processo eseguito. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatti per quando il processo può eseguire e pianificare il lavoro da eseguire quando vengono soddisfatte le condizioni. In questo modo Android per operare in modo intelligente. Ad esempio, possono essere eseguiti in batch le richieste di rete per l'esecuzione di tutti allo stesso tempo per l'utilizzo massimo di overhead coinvolti con la rete.
2. **Che incapsula le operazioni** &ndash; il codice per eseguire le attività in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e sia relativamente facile da modificare la pianificazione se non viene completato il lavoro per qualche motivo.

Il Dispatcher processo Firebase è una libreria da Google che fornisce un'API fluent per semplificare la pianificazione del lavoro in background. Deve essere la sostituzione per la gestione di Cloud di Google. Il Dispatcher processo Firebase include le API seguenti:

* Oggetto `Firebase.JobDispatcher.JobService` è una classe astratta che deve essere esteso con la logica di cui verrà eseguito il processo in background.
* Oggetto `Firebase.JobDispatcher.JobTrigger` dichiara quando il processo deve essere avviato. Questo valore viene in genere espresso come un intervallo di tempo, ad esempio, attendere almeno 30 secondi prima di avviare il processo, ma eseguire il processo entro 5 minuti.
* Oggetto `Firebase.JobDispatcher.RetryStrategy` contiene informazioni su ciò che deve essere eseguita quando un processo non riesce a garantire la corretta esecuzione. La strategia di ripetizione dei tentativi specifica quanto tempo di attesa prima di provare a eseguire nuovamente il processo. 
* Oggetto `Firebase.JobDispatcher.Constraint` è un valore facoltativo che descrive una condizione che deve essere soddisfatti prima di poter eseguire il processo, ad esempio il dispositivo è in una rete unmetered e degli addebiti.
* Il `Firebase.JobDispatcher.Job` è un'API che unifica le API precedenti a un'unità di lavoro che può essere pianificata dal `JobDispatcher`. Il `Job.Builder` classe viene utilizzata per creare un'istanza di un `Job`.
* Oggetto `Firebase.JobDispatcher.JobDispatcher` Usa le API di tre precedente per pianificare il lavoro con il sistema operativo e per fornire un modo per annullare i processi, se necessario.

Per pianificare il lavoro con il Dispatcher processo Firebase, un'applicazione xamarin. Android è necessario incapsulare il codice in un tipo che estende il `JobService` classe. `JobService` ha tre metodi del ciclo di vita che possono essere chiamati nel corso della durata del processo:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Questo metodo è dove il lavoro verrà eseguito e deve sempre essere implementato. Viene eseguito sul thread principale. Questo metodo restituirà `true` se è presente lavoro rimanente, o `false` se il lavoro viene eseguito. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Viene chiamato quando il processo è stato arrestato per ragioni. Il metodo deve restituire `true` se il processo dovrebbe essere riprogrammato per un uso successivo.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Questo metodo viene chiamato quando il `JobService` ha terminato di operazioni asincrone. 

Per pianificare un processo, verrà creata un'istanza dell'applicazione un `JobDispatcher` oggetto. Quindi, un' `Job.Builder` viene usato per creare un `Job` oggetto, che viene fornito per il `JobDispatcher` che tenta di pianificare l'esecuzione del processo.

Questa guida illustra come aggiungere il Dispatcher processo Firebase a un'applicazione xamarin. Android e usarla per pianificare il lavoro in background.

## <a name="requirements"></a>Requisiti

Il Dispatcher processo Firebase richiede il livello API Android 9 o versione successiva. La libreria Firebase processo Dispatcher si basa su alcuni componenti forniti da Google Play Services; il dispositivo deve avere Google Play Services.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso della libreria di Dispatcher Firebase processo in xamarin. Android

Per iniziare a usare il Dispatcher processo Firebase, aggiungere prima la [mobileengagement Xamarin.Firebase.JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al progetto xamarin. Android. Eseguire la ricerca di gestione pacchetti NuGet per la **Xamarin.Firebase.JobDispatcher** pacchetto (che è ancora in versione non definitiva).

Dopo aver aggiunto la libreria Firebase processo Dispatcher, creare un `JobService` classe e quindi pianificare l'esecuzione con un'istanza del `FirebaseJobDispatcher`.


### <a name="creating-a-jobservice"></a>Creazione di un JobService

Tutte le operazioni eseguite dalla libreria di Firebase processo Dispatcher devono essere eseguita in un tipo che estende il `Firebase.JobDispatcher.JobService` classe astratta. Creazione di un `JobService` è molto simile alla creazione di un `Service` con framework Android: 

1. Estendere il `JobService` classe
2. Decorare la sottoclasse con la `ServiceAttribute`. Anche se non è strettamente necessaria, è consigliabile impostare in modo esplicito il `Name` parametro per facilitare il debug di `JobService`. 
3. Aggiungere un `IntentFilter` per dichiarare il `JobService` nel **androidmanifest. XML**. Ciò risulta utile anche la libreria Firebase processo Dispatcher individuare e richiamare il `JobService`.

Il codice seguente è riportato un esempio delle più semplici `JobService` per un'applicazione, usando la libreria TPL per eseguire in modo asincrono alcune operazioni:

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

Prima di qualsiasi lavoro può essere pianificato, è necessario creare un `Firebase.JobDispatcher.FirebaseJobDispatcher` oggetto. Il `FirebaseJobDispatcher` è responsabile della pianificazione una `JobService`. Il frammento di codice seguente è un modo per creare un'istanza di `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Nel frammento di codice precedente, il `GooglePlayDriver` è una classe che consente la `FirebaseJobDispatcher` interagire con alcune delle API di programmazione in Google Play Services nel dispositivo. Il parametro `context` è qualsiasi Android `Context`, ad esempio un'attività. Attualmente le `GooglePlayDriver` è l'unico `IDriver` implementazione nella libreria Firebase processo Dispatcher. 

L'associazione di xamarin. Android per il Dispatcher processo Firebase fornisce un metodo di estensione per creare un `FirebaseJobDispatcher` dal `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una volta il `FirebaseJobDispatcher` è stata creata un'istanza, è possibile creare un `Job` ed eseguire il codice nel `JobService` classe. Il `Job` creando un `Job.Builder` dell'oggetto e verrà descritta nella sezione successiva.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creazione di un Firebase.JobDispatcher.Job con il Job.Builder

Il `Firebase.JobDispatcher.Job` classe è responsabile che incapsula i metadati necessari per eseguire un `JobService`. Oggetto`Job` contiene informazioni quali eventuali vincoli che devono essere soddisfatti prima di poter eseguire il processo, se il `Job` è ricorrente, o i trigger che determinano il processo da eseguire.  Come minimo, una `Job` deve avere una _tag_ (una stringa univoca che identifica il processo per il `FirebaseJobDispatcher`) e il tipo del `JobService` che deve essere eseguito. Il Dispatcher processo Firebase creerà un'istanza di `JobService` quando è possibile eseguire il processo.  Oggetto `Job` viene creato usando un'istanza del `Firebase.JobDispatcher.Job.JobBuilder` classe. 

Il frammento di codice seguente è un esempio più semplice di come creare un `Job` utilizzando l'associazione di xamarin. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

Il `Job.Builder` verranno eseguiti alcuni controlli di convalida di base sui valori di input per il processo. Verrà generata un'eccezione se non è possibile per il `Job.Builder` per creare un `Job`.  Il `Job.Builder` creerà un `Job` con i valori predefiniti seguenti:

* Oggetto `Job`del _durata_ (quanto tempo verrà pianificata per l'esecuzione) è solo fino a quando il dispositivo viene riavviato &ndash; dopo che il dispositivo viene riavviato il `Job` viene perso.
* Oggetto `Job` non è ricorrente &ndash; verrà eseguito solo una volta.
* Oggetto `Job` verrà pianificata per l'esecuzione appena possibile.
* La strategia di ripetizione dei tentativi predefinita per un `Job` consiste nell'usare un' _backoff esponenziale_ (illustrati in modo dettagliato più avanti nella sezione [impostando un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Dopo aver creato il `Job`, deve essere pianificato con il `FirebaseJobDispatcher` prima che venga eseguita. Esistono due metodi per la pianificazione di un `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

Il valore restituito da `FirebaseJobDispatcher.Schedule` sarà uno dei valori interi seguenti:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; Il `Job` è stata pianificata.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Si è verificato un problema sconosciuto che ha impedito la `Job` dall'ultima esecuzione pianificata.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; Un valore non valido `IDriver` è stato usato o `IDriver` in qualche modo non è disponibile. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; Il `Trigger` non era supportata.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; Il servizio non è configurato correttamente o non è disponibile.
 
### <a name="configuring-a-job"></a>Configurazione di un processo

È possibile personalizzare un processo. Esempi di come un processo può essere personalizzato includono quanto segue:

* [Il passaggio di parametri a un processo](#Passing_Parameters_to_a_Job) &ndash; oggetto `Job` può richiedere valori aggiuntivi per svolgere il proprio lavoro, ad esempio download di un file.
* [Impostare i vincoli](#Setting_Constraints) &ndash; potrebbe essere necessario solo eseguire un processo quando vengono soddisfatte determinate condizioni. Ad esempio, eseguire solo un `Job` quando carica il dispositivo. 
* [Specificare quando un `Job` deve essere eseguito](#Setting_Job_Triggers) &ndash; il Dispatcher di processo Firebase consente alle applicazioni di specificare un'ora quando il processo da eseguire.  
* [Dichiarare una strategia di ripetizione dei tentativi per i processi non riusciti](#Setting_a_RetryStrategy) &ndash; oggetto _ripetere strategia_ fornisce materiale sussidiario per il `FirebaseJobDispatcher` sulle operazioni da eseguire con `Jobs` che non vengono completate. 

Ognuno di questi argomenti verrà discusso più nelle sezioni seguenti.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passaggio di parametri a un processo

I parametri vengono passati a un processo tramite la creazione di un `Bundle` che viene passato con la `Job.Builder.SetExtras` metodo:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

Il `Bundle` è accessibile dal `IJobParameters.Extras` proprietà di `OnStartJob` (metodo):

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>L'impostazione di vincoli

I vincoli possono aiutare riduce i costi o della batteria svuotamento del dispositivo. Il `Firebase.JobDispatcher.Constraint` classe definisce questi vincoli come valori interi:

* `Constraint.OnUnmeteredNetwork` &ndash; Eseguire il processo solo quando il dispositivo è connesso a una rete unmetered. Ciò è utile per impedire all'utente di incorrere in addebiti di dati.
* `Constraint.OnAnyNetwork` &ndash; Eseguire il processo in qualsiasi rete è connessa al dispositivo. Se specificato insieme a `Constraint.OnUnmeteredNetwork`, questo valore viene assegnata la priorità.
* `Constraint.DeviceCharging` &ndash; Eseguire il processo solo quando si carica il dispositivo.

I vincoli sono impostati con il `Job.Builder.SetConstraint` metodo: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

Il `JobTrigger` fornisce materiale sussidiario per il sistema operativo su quando deve iniziare il processo. Oggetto `JobTrigger` ha un _esecuzione finestra_ che definisce un'ora pianificata per i casi il `Job` deve essere eseguito. La finestra di esecuzione dispone di un _finestra iniziale_ valore e un _finestra finale_ valore. La finestra di avvio è il numero di secondi che il dispositivo deve attendere prima di eseguire il processo e il valore di finestra finale è il numero massimo di secondi di attesa prima dell'esecuzione di `Job`. 

Oggetto `JobTrigger` possono essere creati con la `Firebase.Jobdispatcher.Trigger.ExecutionWindow` (metodo).  Ad esempio `Trigger.ExecutionWindow(15,60)` significa che il processo da eseguire tra 15 e 60 secondi da quando viene pianificata. Il `Job.Builder.SetTrigger` metodo viene usato per 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

Il valore predefinito `JobTrigger` per un processo è rappresentato dal valore `Trigger.Now`, che consente di eseguire un processo appena possibile dopo l'ultima esecuzione pianificata del...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>L'impostazione di un RetryStrategy

Il `Firebase.JobDispatcher.RetryStrategy` viene usato per specificare la parte di un ritardo di un dispositivo deve usare prima di provare a eseguire di nuovo un processo non riuscito. Oggetto `RetryStrategy` ha un _criterio_, che definisce l'algoritmo di base di tempo verrà usato per pianificare nuovamente il processo non riuscito e una finestra di esecuzione che specifica una finestra in cui il processo deve essere pianificato. Ciò _ripianificazione finestra_ definito dai due valori. Il primo valore è il numero di secondi di attesa prima del rinvio del processo (il _iniziale backoff_ valore), e il secondo numero rappresenta il numero massimo di secondi prima che il processo è necessario eseguire (il _backoff massimo_valore). 
 
I due tipi di criteri di ripetizione dei tentativi sono identificabili tramite questi valori int:

* `RetryStrategy.RetryPolicyExponential` &ndash; Un' _backoff esponenziale_ criteri aumenterà in modo esponenziale il valore di backoff iniziale dopo ogni errore. La prima volta che un processo non riesce, la libreria attenderà l'intervallo di iniziale specificato prima del rinvio del processo &ndash; esempio 30 secondi. La seconda volta che il processo non riesce, la libreria attenderà almeno 60 secondi prima di provare a eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria di 120 secondi di attesa e così via. Il valore predefinito `RetryStrategy` per il Dispatcher processo Firebase libreria è rappresentata dal `RetryStrategy.DefaultExponential` oggetto. Include un backoff iniziale di 30 secondi e un backoff massimo di 3600 secondi.
* `RetryStrategy.RetryPolicyLinear` &ndash; Questa strategia è un _backoff lineare_ che il processo dovrebbe essere riprogrammato per l'esecuzione a intervalli predefiniti (fino a quando non ha esito positivo). Backoff lineare è particolarmente adatto per l'elaborazione che deve essere completate prima possibile o per i problemi che consentirà di risolvere rapidamente se stessi. La libreria Firebase processo Dispatcher definisce un `RetryStrategy.DefaultLinear` che dispone di una finestra ripianificazione di almeno 30 secondi e fino a 3600 secondi.

È possibile definire una classe personalizzata `RetryStrategy` con il `FirebaseJobDispatcher.NewRetryStrategy` (metodo). Accetta tre parametri:

1. `int policy` &ndash; Il _criterio_ è uno dei precedenti `RetryStrategy` valori `RetryStrategy.RetryPolicyLinear`, o `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds` &ndash; Il _backoff iniziale_ un ritardo, in secondi, che è necessario prima di provare a eseguire nuovamente il processo. Il valore predefinito è 30 secondi. 
3. `int maximumBackoffSeconds` &ndash; Il _backoff massimo_ valore dichiara il numero massimo di secondi di ritardo prima di provare a eseguire nuovamente il processo. Il valore predefinito è 3600 secondi. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Annullare un processo

È possibile annullare tutti i processi che sono stati pianificati, o semplicemente un singolo processo con il `FirebaseJobDispatcher.CancelAll()` metodo o il `FirebaseJobDispatcher.Cancel(string)` metodo:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Uno dei due metodi restituirà un valore integer:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; Il processo è stato annullato correttamente.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Errore durante il processo da in corso l'annullamento.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; Il `FirebaseJobDispatcher` è in grado di annullare il processo perché non è valido non `IDriver` disponibili.

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come usare il Dispatcher processo Firebase per operare in modo intelligente in background. Illustrata la procedura per incapsulare il lavoro deve essere eseguita come un `JobService` e su come usare il `FirebaseJobDispatcher` pianificare il lavoro, specificare i criteri con un `JobTrigger` e modalità di gestione degli errori con un `RetryStrategy`.


## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Firebase.JobDispatcher in NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-processi-dispatcher su GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Intelligent pianificazione dei processi](https://developer.android.com/topic/performance/scheduling.html)
- [Android della batteria e ottimizzazioni della memoria - 2016 dei / o Google (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
