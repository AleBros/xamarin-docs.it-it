---
title: Dispatcher processo firebase
description: Questa guida viene descritto come pianificare il lavoro in background utilizza la libreria Firebase processo Dispatcher da Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 06/05/2018
ms.openlocfilehash: 0d512342f1c978c84341d20c298a9fa750800d84
ms.sourcegitcommit: 5db075bdd0b62d5d1d1567c267303a6a1888c8f2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806803"
---
# <a name="firebase-job-dispatcher"></a>Dispatcher processo firebase

_Questa guida viene descritto come pianificare il lavoro in background utilizza la libreria Firebase processo Dispatcher da Google._


## <a name="overview"></a>Panoramica

Uno dei modi migliori per mantenere la reattività all'utente di un'applicazione Android è per assicurarsi che il lavoro complesso o a esecuzione prolungata viene eseguito in background. Tuttavia, è importante che operazioni in background non influiranno negativamente sull'esperienza dell'utente con il dispositivo. 

Ad esempio, un processo in background potrebbe eseguire il polling di un sito Web tre o quattro minuti per eseguire query per le modifiche a un determinato set di dati. Può sembrare un passo grave, ma avrebbe un impatto disastroso sulla durata della batteria. L'applicazione verrà ripetutamente riattivare il dispositivo, elevare la CPU a uno stato di alimentazione superiore, accendere la radio, le richieste di rete e quindi elaborare i risultati. Ottiene il peggiore perché il dispositivo verrà immediatamente non spegnere e ripristinare lo stato di inattività di basso consumo. Operazioni in background pianificata in modo inadeguato potrebbero inavvertitamente tenere il dispositivo in stato di alimentazione superflua ed eccessiva. Questa attività apparentemente innocuo (polling di un sito Web) verrà eseguito il rendering del dispositivo inutilizzabile in un periodo di tempo relativamente breve.

Android fornisce le API seguenti per facilitare l'esecuzione di lavoro in background ma da soli non sono sufficienti per la pianificazione dei processi intelligenti. 

* **[Servizi preventivi](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; con finalità di servizi sono ideali per l'esecuzione del lavoro, ma non consentono di pianificare il lavoro.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; queste API consentono solo di lavoro essere pianificato ma non si specifica alcuna possibilità di eseguire effettivamente il lavoro. Inoltre, il AlarmManager consente solo limiti di tempo in base che si intende generare un avviso a una determinata ora o una volta trascorso un determinato periodo di tempo. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule il è un'API grande che funziona con il sistema operativo per pianificare i processi. Tuttavia è disponibile solo per tali App Android destinati a livello API 21 o superiore. 
* **[Trasmissione ricevitori](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; app di Android è possibile configurare broadcast ricevitori per eseguire il lavoro in risposta a eventi a livello di sistema o i tipi. Tuttavia, ricevitori broadcast non forniscono alcun controllo dell'esecuzione del processo deve essere. Limitano inoltre le modifiche nel sistema operativo Android quando è funzionerà ricevitori broadcast o i tipi di lavoro in grado di rispondere a. 

Esistono due funzionalità fondamentali per in modo efficiente l'esecuzione dell'attività in background (talvolta detto una _processo in background_ o una _processo_):

1. **In modo intelligente pianificazione del lavoro** &ndash; è importante che quando un'applicazione esegue operazioni in background che esegue l'operazione come buona cittadinanza. Idealmente, l'applicazione non deve richiedere che un processo eseguito. Al contrario, l'applicazione deve specificare le condizioni che devono essere soddisfatte per quando il processo può eseguire e quindi pianificare il lavoro da eseguire quando vengono soddisfatte le condizioni. In questo modo Android lavorare in modo intelligente. Ad esempio, possono essere eseguiti in batch le richieste di rete per l'esecuzione di tutti allo stesso tempo per l'utilizzo massimo di overhead coinvolti con la rete.
2. **Che incapsula il lavoro** &ndash; il codice per eseguire le operazioni in background deve essere incapsulato in un componente discreto che può essere eseguito indipendentemente dall'interfaccia utente e che sia relativamente facile da modificare la pianificazione se non viene eseguito il lavoro per qualche motivo.

Il Dispatcher processo Firebase è una libreria da Google che fornisce un'API intuitiva per semplificare la pianificazione del lavoro in background. È progettato per essere la sostituzione per la gestione di Cloud di Google. Il Dispatcher processo Firebase include le API seguenti:

* Oggetto `Firebase.JobDispatcher.JobService` è una classe astratta che deve essere estesa con la logica di cui verrà eseguito il processo in background.
* Oggetto `Firebase.JobDispatcher.JobTrigger` dichiara quando il processo deve essere avviato. Questo valore viene in genere espresso come un intervallo di tempo, ad esempio, attendere almeno 30 secondi prima di avviare il processo, ma eseguire il processo entro 5 minuti.
* Oggetto `Firebase.JobDispatcher.RetryStrategy` contiene informazioni sulle azioni da intraprendere quando si verifica un errore di un processo venga eseguito correttamente. La strategia di tentativi specifica il tempo di attesa prima di provare a eseguire nuovamente il processo. 
* Oggetto `Firebase.JobDispatcher.Constraint` è un valore facoltativo che descrive una condizione che deve essere soddisfatte prima di esegue il processo, ad esempio il dispositivo è in una rete unmetered o ad addebitare i costi.
* Il `Firebase.JobDispatcher.Job` è un'API che riunisce le API precedenti a un'unità di lavoro che possono essere pianificati per il `JobDispatcher`. Il `Job.Builder` classe viene utilizzata per creare un'istanza di un `Job`.
* Oggetto `Firebasee.JobDispatcher.JobDispatcher` utilizza le API di tre precedente per pianificare il lavoro con il sistema operativo e per fornire un modo per annullare i processi, se necessario.

Per pianificare il lavoro con il Dispatcher processo Firebase, un'applicazione di xamarin è necessario incapsulare il codice in un tipo che estende la `JobService` classe. `JobService` dispone di tre metodi del ciclo di vita che può essere chiamato nel corso della durata del processo:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Questo metodo è in cui si verificherà il lavoro e deve sempre essere implementato. Viene eseguito nel thread principale. Questo metodo restituirà `true` se è presente, lavoro rimanente, o `false` se l'operazione viene eseguita. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Viene chiamato quando il processo viene arrestato per qualche motivo. Il metodo deve restituire `true` se il processo deve essere riprogrammato per un momento successivo.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Questo metodo viene chiamato quando il `JobService` ha terminato di operazioni asincrone. 

Per pianificare un processo, l'applicazione crea un'istanza di un `JobDispatcher` oggetto. Quindi, un `Job.Builder` viene utilizzato per creare un `Job` oggetto, che viene fornito per il `JobDispatcher` che tenta di pianificare l'esecuzione del processo.

Questa Guida verrà illustrate le procedure aggiungere il Dispatcher processo Firebase a un'applicazione di xamarin e utilizzarla per pianificare operazioni in background.

## <a name="requirements"></a>Requisiti

Il Dispatcher processo Firebase richiede il livello API Android 9 o versione successiva. La libreria Firebase processo Dispatcher si basa su alcuni componenti forniti da Google Play Services; il dispositivo deve disporre di Google Play Services.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Utilizzo della libreria di Dispatcher processo Firebase in xamarin

Per iniziare a utilizzare il Dispatcher processo Firebase, aggiungere il [pacchetto Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al progetto xamarin. Ricerca di gestione pacchetti NuGet per la **Xamarin.Firebase.JobDispatcher** pacchetto (che è ancora in versione non definitiva).

Dopo aver aggiunto la libreria Firebase Dispatcher di processo, creare un `JobService` classe e quindi pianificare l'esecuzione con un'istanza di `FirebaseJobDispatcher`.


### <a name="creating-a-jobservice"></a>Creazione di un JobService

Tutte le operazioni eseguite dalla libreria Firebase processo Dispatcher devono essere effettuata entro un tipo che estende la `Firebase.JobDispatcher.JobService` classe astratta. Creazione di un `JobService` è molto simile alla creazione di un `Service` con Android framework: 

1. Estendere la `JobService` classe
2. Decorare la sottoclasse con la `ServiceAttribute`. Benché non sia obbligatorio, si consiglia di impostare in modo esplicito il `Name` parametro per facilitare il debug di `JobService`. 
3. Aggiungere un `IntentFilter` per dichiarare il `JobService` nel **AndroidManifest.xml**. Ciò consentirà inoltre la libreria Firebase processo Dispatcher individuare e richiamare il `JobService`.

Il codice riportato di seguito è riportato un esempio delle più semplici `JobService` per un'applicazione, utilizzando la libreria TPL per eseguire in modo asincrono alcune operazioni:

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

Prima di tutte le operazioni possono essere pianificate, è necessario creare un `Firebase.JobDispatcher.FirebaseJobDispatcher` oggetto. Il `FirebaseJobDispatcher` è responsabile della pianificazione un `JobService`. Frammento di codice seguente è un modo per creare un'istanza di `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Nel frammento di codice precedente, il `GooglePlayDriver` è una classe che consente di `FirebaseJobDispatcher` interagire con alcune delle API di programmazione in Google Play Services nel dispositivo. Il parametro `context` è qualsiasi Android `Context`, ad esempio un'attività. Attualmente il `GooglePlayDriver` è l'unico `IDriver` implementazione nella libreria Firebase processo Dispatcher. 

L'associazione di xamarin per il Dispatcher processo Firebase fornisce un metodo di estensione per creare un `FirebaseJobDispatcher` dal `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una volta il `FirebaseJobDispatcher` è stata creata un'istanza, è possibile creare un `Job` ed eseguire il codice `JobService` classe. Il `Job` creato da un `Job.Builder` dell'oggetto e verrà descritta nella sezione successiva.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creazione di un Firebase.JobDispatcher.Job con il Job.Builder

Il `Firebase.JobDispatcher.Job` classe è responsabile per incapsulare i metadati necessari per eseguire un `JobService`. Oggetto`Job` contiene informazioni quali tutti i vincoli che devono essere soddisfatte prima di esegue il processo, se il `Job` è ricorrente, o i trigger che faranno sì che il processo da eseguire.  Come minimo, un `Job` deve avere un _tag_ (una stringa univoca che identifica il processo per il `FirebaseJobDispatcher`) e il tipo del `JobService` che deve essere eseguito. Il Dispatcher processo Firebase verrà creata un'istanza di `JobService` quando è necessario eseguire il processo.  A `Job` viene creato utilizzando un'istanza di `Firebase.JobDispatcher.Job.JobBuilder` classe. 

Il seguente frammento di codice è il più semplice esempio di come creare un `Job` utilizzando l'associazione di xamarin:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

Il `Job.Builder` esegue alcuni controlli di convalida di base sui valori di input per il processo. Verrà generata un'eccezione se non è possibile per il `Job.Builder` per creare un `Job`.  Il `Job.Builder` creerà un `Job` con i valori predefiniti seguenti:

* A `Job`del _durata_ (quanto tempo verrà pianificata per l'esecuzione) è solo fino a quando il dispositivo viene riavviato &ndash; dopo che il dispositivo viene riavviato il `Job` viene perso.
* Oggetto `Job` non sia ricorrente &ndash; funzionerà solo una volta.
* Oggetto `Job` verrà pianificato per l'esecuzione appena possibile.
* La strategia di tentativi predefinito per un `Job` consiste nell'usare un _backoff esponenziale_ (descritti in modo dettagliato più avanti nella sezione [impostando un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Pianificazione di un processo

Dopo aver creato il `Job`, deve essere pianificata con il `FirebaseJobDispatcher` prima di eseguirlo. Esistono due metodi per la pianificazione di un `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

Il valore restituito da `FirebaseJobDispatcher.Schedule` sarà uno dei valori interi seguenti:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; Il `Job` è stata pianificata.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Si è verificato un problema sconosciuto che ha impedito la `Job` da pianificata.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; Un valido `IDriver` è stata utilizzata o `IDriver` in qualche modo non è disponibile. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; Il `Trigger` non è supportato.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; Il servizio non è configurato correttamente o non è disponibile.
 
### <a name="configuring-a-job"></a>Configurazione di un processo

È possibile personalizzare un processo. Esempi di come un processo può essere personalizzato includono:

* [Passaggio di parametri a un processo](#Passing_Parameters_to_a_Job) &ndash; A `Job` potrebbe richiedere valori aggiuntivi per eseguire il lavoro, ad esempio download di un file.
* [Impostare vincoli](#Setting_Constraints) &ndash; potrebbe essere necessario eseguire un processo solo quando vengono soddisfatte determinate condizioni. Ad esempio, eseguire solo un `Job` carica quando il dispositivo. 
* [Specificare quando un `Job` deve essere eseguito](#Setting_Job_Triggers) &ndash; il Dispatcher di processo Firebase consente alle applicazioni di specificare un'ora quando si desidera eseguire il processo.  
* [Dichiarare una strategia di ripetizione per i processi non riusciti](#Setting_a_RetryStrategy) &ndash; A _della strategia di tentativi_ vengono fornite indicazioni per la `FirebaseJobDispatcher` sulle operazioni da eseguire con `Jobs` che non riescono a completare. 

Ognuno di questi argomenti verrà descritte più nelle sezioni seguenti.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-jarameters-to-a-job"></a>Jarameters passando a un processo

I parametri vengono passati a un processo tramite la creazione di un `Bundle` passato con la `Job.Builder.SetExtras` metodo:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

Il `Bundle` è accessibile dal `IJobParameters.Extras` proprietà il `OnStartJob` metodo:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Impostazione dei vincoli

Consentono di vincoli riduce i costi o batteria svuotamento nel dispositivo. La `Firebase.JobDispatcher.Constraint` classe definisce i vincoli come valori integer:

* `Constraint.OnUnmeteredNetwork` &ndash; Consente di eseguire solo il processo quando il dispositivo è connesso a una rete unmetered. Ciò è utile per impedire all'utente di dover sostenere costi di dati.
* `Constraint.OnAnyNetwork` &ndash; Eseguire il processo in qualsiasi rete il dispositivo è connesso. Se specificato insieme a `Constraint.OnUnmeteredNetwork`, questo valore viene assegnata la priorità.
* `Constraint.DeviceCharging` &ndash; Eseguire il processo solo quando si carica il dispositivo.

I vincoli sono impostati con il `Job.Builder.SetConstraint` metodo: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

Il `JobTrigger` vengono fornite indicazioni per il sistema operativo sull'inizio del processo. A `JobTrigger` ha un _esecuzione finestra_ che definisce un'ora pianificata per quando il `Job` deve essere eseguito. La finestra di esecuzione ha un _avvia finestra_ valore e un _finestra finale_ valore. Finestra di avvio è il numero di secondi che il dispositivo deve attendere prima di eseguire il processo e il valore di finestra finale è il numero massimo di secondi di attesa prima di eseguire il `Job`. 

Oggetto `JobTrigger` possono essere creati con il `Firebase.Jobdispatcher.Trigger.ExecutionWindow` metodo.  Ad esempio `Trigger.ExecutionWindow(15,60)` significa che il processo da eseguire tra 15 e 60 secondi da quando è pianificata. Il `Job.Builder.SetTrigger` metodo viene utilizzato per 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

Il valore predefinito `JobTrigger` per un processo è rappresentato dal valore `Trigger.Now`, che consente di eseguire un processo appena possibile dopo la pianificazione...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>L'impostazione di un RetryStrategy

Il `Firebase.JobDispatcher.RetryStrategy` viene utilizzato per specificare la quantità di un ritardo di un dispositivo deve utilizzare prima di provare a eseguire di nuovo un processo non riuscito. Oggetto `RetryStrategy` ha un _criteri_, che definisce l'algoritmo di base di tempo da utilizzare per pianificare nuovamente il processo non riuscito e una finestra di esecuzione che specifica una finestra in cui il processo pianificato. Questo _finestra riprogrammazione_ definito dai due valori. Il primo valore è il numero di secondi di attesa prima di ripianificazione del processo (il _backoff iniziale_ valore), e il secondo numero è il numero massimo di secondi prima di esegua il processo (il _backoff massimo_valore). 
 
I due tipi di criteri di tentativi vengono identificati questi valori int:

* `RetryStrategy.RetryPolicyExponential` &ndash; Un _backoff esponenziale_ criteri aumenterà in modo esponenziale il valore di backoff iniziale dopo ogni errore. La prima volta che un processo non riesce, la libreria attenderà l'intervallo di _initial specificato prima di ripianificazione del processo &ndash; esempio 30 secondi. La seconda volta che il processo non riesce, la libreria dovrà attendere almeno 60 secondi prima di tentare di eseguire il processo. Dopo il terzo tentativo non riuscito, la libreria di 120 secondi di attesa e così via. Il valore predefinito `RetryStrategy` per il Dispatcher processo Firebase libreria è rappresentata dal `RetryStrategy.DefaultExponential` oggetto. Dispone un backoff iniziale pari a 30 secondi e un backoff massimo di 3600 secondi.
* `RetryStrategy.RetryPolicyLinear` &ndash; Questa strategia è un _backoff lineare_ che dovrebbe essere riprogrammato il processo per l'esecuzione di impostare gli intervalli (fino a quando non ha esito positivo). Backoff lineare è ideale per l'elaborazione che deve essere completate presto o per i problemi verranno risolti rapidamente. La libreria Firebase processo Dispatcher definisce un `RetryStrategy.DefaultLinear` che dispone di una finestra riprogrammazione di almeno 30 secondi e fino a 3600 secondi.

È possibile definire un oggetto personalizzato `RetryStrategy` con il `FirebaseJobDispatcher.NewRetryStrategy` metodo. Il metodo accetta tre parametri:

1. `int policy` &ndash; Il _criteri_ è uno dei precedente `RetryStrategy` valori, `RetryStrategy.RetryPolicyLinear`, o `RetryStrategy.RetryPolicyExponential`.
2. `int intialBackoffSeconds` &ndash; Il _backoff iniziale_ un ritardo, in secondi, che è necessario prima di provare a eseguire nuovamente il processo. Il valore predefinito è 30 secondi. 
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

È possibile annullare tutti i processi che sono stati pianificati o solo un singolo processo tramite il `FirebaseJobDispatcher.CancelAll()` metodo o la `FirebaseJobDispatcher.Cancel(string)` metodo:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Il metodo restituirà un valore integer:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; Processo annullato.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Un errore ha impedito il processo di annullamento.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; Il `FirebaseJobDispatcher` è in grado di annullare il processo non è valido non `IDriver` disponibili.

## <a name="summary"></a>Riepilogo

Questa guida viene spiegato come utilizzare il Dispatcher processo Firebase per lavorare in modo intelligente in background. Illustrata la procedura per incapsulare il lavoro deve essere eseguita come un `JobService` e come utilizzare la `FirebaseJobDispatcher` pianificare il lavoro, specificare i criteri con un `JobTrigger` e modalità di gestione di errori con un `RetryStrategy`.


## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Firebase.JobDispatcher in NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [dispatcher di processo firebase su GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Intelligente pianificazione dei processi](https://developer.android.com/topic/performance/scheduling.html)
- [Android batteria e ottimizzazioni memoria - 2016 dei / o Google (video)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
