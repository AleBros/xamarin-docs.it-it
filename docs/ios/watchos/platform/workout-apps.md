---
title: App allenamenti
description: Questo articolo descrive i miglioramenti Apple ha apportate alle App allenamenti watchOS 3 e come implementarli in Xamarin.
ms.topic: article
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: f375f58215fbef13298e31b03b969c9388d2bfa2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="workout-apps"></a>App allenamenti

_Questo articolo descrive i miglioramenti Apple ha apportate alle App allenamenti watchOS 3 e come implementarli in Xamarin._


Nuovo per watchOS 3, allenamenti correlati App hanno la possibilità di eseguire in background nel Apple Watch e accedere ai dati HealthKit. Le app iOS 10 basato su padre ha inoltre la possibilità di avviare l'app watchOS 3 base senza intervento dell'utente.

Gli argomenti seguenti vengono descritti nel dettaglio:

## <a name="about-workout-apps"></a>Sulle App allenamenti

Possono essere elevata dedicati, gli utenti delle App adeguatezza e allenamenti dedicare diverse ore del giorno verso gli obiettivi di integrità e adeguatezza. Di conseguenza, si aspettano App reattiva e facile da utilizzare in modo accurato raccogliere e visualizzare i dati e di integrazione con l'integrità di Apple.

Un'applicazione adeguatezza o allenamenti ben progettata consente agli utenti le relative attività per raggiungere gli obiettivi di idoneità del grafico. Tramite l'Apple Watch, App adeguatezza e allenamenti hanno accesso immediato alla frequenza di cuore, il rilevamento di attività e velocità calorie.

[ ![](workout-apps-images/workout01.png "Esempio di app adeguatezza e allenamenti")](workout-apps-images/workout01.png)

Novità di watchOS 3, _in esecuzione in Background_ allenamenti offre la possibilità di eseguire in background nel Apple Watch e accedere ai dati HealthKit di App correlati.

Questo documento è stata introdotta la funzionalità in esecuzione in Background, coprire il ciclo di vita app allenamenti e Mostra come l'utente può comportare un'app allenamenti _attività anelli_ sul Apple Watch.

## <a name="about-workout-sessions"></a>Sulle sessioni allenamenti

È il cuore di ogni app allenamenti un _sessione allenamenti_ (`HKWorkoutSession`) che l'utente può avviare e arrestare. L'API di sessione allenamenti è facile da implementare e offre diversi vantaggi a un'app allenamenti, ad esempio:

- Il movimento e calorie masterizzare il rilevamento in base al tipo di attività.
- Contributo automatico anelli attività dell'utente.
- Mentre in una sessione, l'applicazione verrà visualizzata automaticamente ogni volta che l'utente attiva il dispositivo (in base alla loro polso generazione o l'interazione con l'Apple Watch).

## <a name="about-background-running"></a>Sull'esecuzione di sfondo

Come descritto in precedenza, con watchOS 3 è possibile impostare un'app allenamenti per l'esecuzione in background. Utilizzato in Background che esegue un'app allenamenti può elaborare i dati da sensori dell'Apple Watch durante l'esecuzione in background. Ad esempio, un'applicazione possa continuare a monitorare frequenza cuore dell'utente, anche se non viene più visualizzato sullo schermo.

In esecuzione in background consente inoltre di presentare all'utente in qualsiasi momento durante una sessione attiva di allenamenti, ad esempio l'invio di un avviso per informare l'utente di avanzamento corrente tattili feedback in tempo reale.

Inoltre, in esecuzione in Background consente all'app aggiornare rapidamente la relativa interfaccia utente, pertanto l'utente ha i dati più recenti quando essi riepilogo rapidamente al loro Apple Watch.

Per mantenere prestazioni elevate in Apple Watch, un'applicazione di espressioni di controllo mediante in esecuzione in Background deve limitare la quantità di operazioni in background per conservare la batteria. Se un'applicazione utilizza troppe risorse della CPU in background, possono essere sospesi da watchOS.

### <a name="enabling-background-running"></a>L'abilitazione in esecuzione in Background

Per abilitare l'esecuzione in Background, procedere come segue:

1. Nel **Esplora**, fare doppio clic dell'app iPhone complementare di espressioni di controllo dell'estensione `Info.plist` file per aprirlo e modificarlo.
2. Passare il **origine** Vista: 

    [ ![](workout-apps-images/plist01.png "La visualizzazione origine")](workout-apps-images/plist01.png)
3. Aggiungere una nuova chiave denominata `WKBackgroundModes` e impostare il **tipo** a `Array`: 

    [ ![](workout-apps-images/plist02.png "Aggiungere una nuova chiave denominata WKBackgroundModes")](workout-apps-images/plist02.png)
4. Aggiungere un nuovo elemento nella matrice con il **tipo** di `String` e il valore `workout-processing`: 

    [ ![](workout-apps-images/plist03.png "Aggiungere un nuovo elemento nella matrice con il tipo di stringa e un valore di allenamenti elaborazione")](workout-apps-images/plist03.png)
5. Salvare le modifiche apportate al file.

## <a name="starting-a-workout-session"></a>Avviare una sessione di allenamenti

Esistono tre passaggi principali per avviare una sessione di allenamenti:

[ ![](workout-apps-images/workout02.png "I tre passaggi principali per avviare una sessione di allenamenti")](workout-apps-images/workout02.png)

1. L'applicazione deve richiedere l'autorizzazione per accedere ai dati in HealthKit.
2. Creare un oggetto di configurazione allenamenti per il tipo di allenamenti in corso l'avvio.
3. Creare e avviare una sessione di allenamenti utilizzando la configurazione allenamenti appena creato.

### <a name="requesting-authorization"></a>La richiesta di autorizzazione

Prima che un'app possa accedere ai dati dell'utente HealthKit, deve richiedere e ricevere l'autorizzazione da parte dell'utente. A seconda della natura dell'app allenamenti potrebbe avere i seguenti tipi di richieste:

- Autorizzazione per la scrittura di dati:
    - Allenamenti
- Autorizzazione alla lettura di dati:
    - Energia masterizzata
    - distanza
    - Frequenza di cuore  

Prima di un'applicazione può richiedere l'autorizzazione, deve essere configurato per l'accesso HealthKit.

Seguire questa procedura:

1. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
2. Scorrere fino alla fine e controllare **HealthKit abilitare**: 

    [ ![](workout-apps-images/auth01.png "Controllo abilitazione HealthKit")](workout-apps-images/auth01.png)
3. Salvare le modifiche apportate al file.
4. Seguire le istruzioni il [esplicita ID dell'App e profilo di Provisioning](~/ios/platform/healthkit.md) e [associando ID App e il Provisioning di profilo con l'App di xamarin](~/ios/platform/healthkit.md) sezioni del [Introduzione a HealthKit](~/ios/platform/healthkit.md) articolo correttamente il provisioning dell'applicazione.
5. Infine, utilizzare le istruzioni nel [programmazione Kit integrità](~/ios/platform/healthkit.md) e [richiesta di autorizzazione da utente](~/ios/platform/healthkit.md) sezioni del [Introduzione a HealthKit](~/ios/platform/healthkit.md) articolo a richiesta autorizzazione per l'accesso dell'archivio dati HealthKit dell'utente.

### <a name="setting-the-workout-configuration"></a>Impostazione della configurazione allenamenti

Sessioni allenamenti vengono create utilizzando un oggetto di configurazione allenamenti (`HKWorkoutConfiguration`) che specifica il tipo di allenamenti (ad esempio `HKWorkoutActivityType.Running`) e il percorso allenamenti (ad esempio `HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Creazione di un delegato di sessione allenamenti 

Per gestire gli eventi che possono verificarsi durante una sessione allenamenti, l'app sarà necessario creare un'istanza del delegato di sessione allenamenti. Aggiungere una nuova classe al progetto e di base viene disattivata la `HKWorkoutSessionDelegate` classe. Per l'esempio di un'esecuzione all'esterno, potrebbe essere simile al seguente:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

Questa classe crea diversi eventi che vengono generati come lo stato delle modifiche allenamenti sessione (`DidChangeToState`) e se la sessione allenamenti ha esito negativo (`DidFail`). 

### <a name="creating-a-workout-session"></a>Creazione di una sessione allenamenti

Utilizzando la configurazione di allenamenti e un delegato di sessione allenamenti creato in precedenza per creare una nuova sessione allenamenti e avviarlo sull'archivio di HealthKit predefinito dell'utente:

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Se l'app viene avviata la sessione allenamenti e l'utente passa alla prima espressione di controllo, verrà visualizzata una piccola icona verde "in esecuzione man" sopra il tipo di carattere:

[ ![](workout-apps-images/workout03.png "Un'icona di man in esecuzione verde piccoli, visualizzata sopra il tipo di carattere")](workout-apps-images/workout03.png)

Se l'utente tocca l'icona, essi verranno eseguite nuovamente all'app.

## <a name="data-collection-and-control"></a>Raccolta dei dati e controllo

Dopo una sessione allenamenti è stata configurata e avviata, sarà necessario l'app raccogliere dati relativi alla sessione (ad esempio tasso di base dell'utente) e controllare lo stato della sessione:

[ ![](workout-apps-images/workout04.png "Diagramma di controllo e la raccolta dei dati")](workout-apps-images/workout04.png)

1. **Esempi di osservazione** -l'app sarà necessario recuperare informazioni da HealthKit che verranno elaborati e visualizzati all'utente.
2. **Osservare gli eventi** -l'app sarà necessario rispondere agli eventi generati da HealthKit o dall'interfaccia utente dell'applicazione (ad esempio, l'utente la sospensione di allenamenti).
3. **Immettere lo stato di esecuzione** -sessione è stata avviata ed è attualmente in esecuzione.
4. **Immettere lo stato di sospensione** -l'utente ha sospeso la sessione corrente di allenamenti e può essere riavviata in un secondo momento. L'utente può passare tra gli stati di sospensione e in esecuzione più volte in una singola sessione allenamenti.
5. **Terminare la sessione allenamenti** : in qualsiasi momento, l'utente può terminare la sessione di allenamenti o può scadere e terminare autonomamente se fosse accinge a consumo (ad esempio, un'esecuzione di miglia due).

Il passaggio finale consiste per salvare i risultati della sessione allenamenti all'archivio dati HealthKit dell'utente.

### <a name="observing-healthkit-samples"></a>Osservare gli esempi HealthKit

L'app sarà necessario aprire un _Query di oggetto di ancoraggio_ per ognuno dei dati HealthKit punti che è interessato, ad esempio tasso cuore o energia active masterizzata. Per ogni punto dati osservata, un gestore aggiornamento dovrà essere creato per acquisire nuovi dati al momento della trasmissione per l'app.

Da questi punti dati, è possibile che l'app totali (ad esempio la distanza di esecuzione totale) e aggiornare di interfaccia utente come richiesto. Inoltre, l'app può inviare una notifica agli utenti quando hanno raggiunto un obiettivo specifico o il risultato, ad esempio il completamento di miglia avanti di un'esecuzione.

Esaminare il codice di esempio seguente:

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

Crea un predicato per impostare la data di avvio che desidera ottenere dati per l'utilizzo di `GetPredicateForSamples` metodo. Crea un set di dispositivi per inserire informazioni HealthKit dall'utilizzo di `GetPredicateForObjectsFromDevices` (metodo), in questo caso solo il locale Apple Watch (`HKDevice.LocalDevice`). I due predicati sono combinati in un predicato composta (`NSCompoundPredicate`) utilizzando il `CreateAndPredicate` metodo.

Un nuovo `HKAnchoredObjectQuery` viene creato per il punto dati desiderato (in questo caso `HKQuantityTypeIdentifier.ActiveEnergyBurned` per il punto dati masterizzata energia attiva), alcun limite sulla quantità di dati restituiti (`HKSampleQuery.NoLimit`) e viene definito un gestore di aggiornamento per gestire being di dati restituito all'app da HealthKit. 

Il gestore di aggiornamento verrà chiamato ogni volta che nuovi dati viene recapitati all'app per il punto dati specificato. Se viene restituito alcun errore, l'app può in modo sicuro la lettura dei dati, verificare tutti i calcoli necessari e aggiornare relativa interfaccia utente come richiesto.

Il codice esegue un ciclo su tutti gli esempi (`HKSample`) restituiti nel `addedObjects` di matrice e il cast a un campione di quantità (`HKQuantitySample`). Viene quindi ottenuto il valore double di esempio come un joule (`HKUnit.Joule`) e lo accumula al totale di energia active masterizzata per l'allenamenti in esecuzione e aggiorna l'interfaccia utente.

### <a name="achieved-goal-notification"></a>Notifica ottenuti obiettivo

Come accennato in precedenza, quando l'utente raggiunge un obiettivo nell'app allenamenti (ad esempio, il completamento di miglia prima di un'esecuzione), è possibile inviare commenti e suggerimenti tattili all'utente tramite il motore Taptic. L'app deve aggiornare anche di interfaccia utente a questo punto, poiché l'utente genererà molto probabilmente loro polso per visualizzare l'evento che viene richiesto il feedback.

Per riprodurre il feedback tattili, utilizzare il codice seguente:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Analisi eventi

Gli eventi sono di tipo timestamp che l'app è possibile utilizzare per evidenziare determinati momenti durante allenamenti dell'utente. Alcuni eventi verranno create direttamente dall'app e salvati nell'allenamenti e alcuni eventi verranno creati automaticamente da HealthKit.

Per osservare gli eventi creati da HealthKit, l'applicazione eseguirà l'override di `DidGenerateEvent` metodo il `HKWorkoutSessionDelegate`:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

Apple ha aggiunto i seguenti nuovi tipi di evento watchOS 3:

- `HKWorkoutEventType.Lap` -Sono per gli eventi che suddivide l'allenamenti in parti uguali distanza. Ad esempio, per contrassegnare un'introduzione ad una traccia mentre è in esecuzione.
- `HKWorkoutEventType.Marker` -Sono all'interno di allenamenti per arbitrari punti di interesse. Ad esempio, quando si raggiunge un punto specifico nella route di un'esecuzione esterni.

Questi nuovi tipi possono essere creati dall'app e archiviati nell'allenamenti per un utilizzo successivo nella creazione di grafici e le statistiche.

Per creare un marcatore di eventi, eseguire le operazioni seguenti:

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

Questo codice crea una nuova istanza di un marcatore di eventi (`HKWorkoutEvent`) e lo salva in una raccolta privata di eventi (che verranno successivamente scritti nella sessione allenamenti) e avvisa l'utente dell'evento tramite haptics.

### <a name="pausing-and-resuming-workouts"></a>Sospensione e ripresa allenamenti

In qualsiasi punto in una sessione allenamenti, l'utente può sospendere l'allenamenti temporaneamente e riprenderla in un secondo momento. Ad esempio, si potrebbe interrompersi un'esecuzione interni per eseguire una chiamata importanti e riprendere l'esecuzione dopo la chiamata è stata completata.

Interfaccia utente dell'applicazione deve fornire un modo per sospendere e riprendere l'allenamenti (chiamando HealthKit) in modo che l'Apple Watch possibile risparmiare spazio di alimentazione e i dati mentre l'utente ha sospeso la loro attività. Inoltre, l'app deve ignorare eventuali nuovi punti dati che possono essere ricevuti quando la sessione allenamenti è in stato di sospensione.

HealthKit risponderà per sospendere e riprendere le chiamate tramite la generazione di eventi di sospensione e ripresa. Durante la sospensione della sessione allenamenti non nuovi eventi o dati verranno inviati all'app da HealthKit fino a quando la sessione viene ripresa.

Per sospendere e riprendere una sessione allenamenti, utilizzare il codice seguente:

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

Gli eventi di sospensione e ripresa che verranno generati da HealthKit possono essere gestiti da si esegue l'override di `DidGenerateEvent` metodo il `HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>Eventi di movimento

Anche nuovi watchOS 3, sono il movimento in pausa (`HKWorkoutEventType.MotionPaused`) e la ripresa di movimento (`HKWorkoutEventType.MotionResumed`) eventi. Questi eventi vengono generati automaticamente da HealthKit durante accinge in esecuzione quando l'utente avvia e Arresta lo spostamento.

Quando l'app riceve un evento sospeso di movimento, è necessario arrestare la raccolta dei dati fino a quando l'utente riprende movimento e viene ricevuto l'evento movimento riprende. App per le app non sospendere la sessione allenamenti in risposta a un evento sospeso di movimento.

> [!IMPORTANT]
> **Nota:** gli eventi di movimento sospesa e ripresa di movimento sono supportati solo per il tipo di attività RunningWorkout (`HKWorkoutActivityType.Running`).

Nuovamente, questi eventi possono essere gestiti eseguendo l'override di `DidGenerateEvent` metodo il `HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>Termina e il salvataggio della sessione allenamenti

Quando l'utente ha completato li allenamenti, l'app sarà necessario terminare la sessione corrente di allenamenti e salvarlo nel database HealthKit. Allenamenti salvati HealthKit verranno automaticamente visualizzati nell'elenco attività allenamenti.

Nuovo in iOS 10, incluso l'elenco di elenco attività allenamenti nonché iPhone dell'utente. Pertanto, anche se non è nelle vicinanze di Apple Watch, l'allenamenti verranno visualizzato sul telefono.

Allenamenti che includono i campioni di energia aggiornerà anello spostare dell'utente nell'applicazione di attività in modo da app di terze parti 3rd ora può contribuire a obiettivi spostamento giornalieri dell'utente.

I passaggi seguenti sono necessari per terminare e salvare una sessione di allenamenti:

[ ![](workout-apps-images/workout05.png "Termina e il salvataggio del diagramma di sessione allenamenti")](workout-apps-images/workout05.png)

1. L'app in primo luogo, sarà necessario terminare la sessione allenamenti.
2. La sessione allenamenti HealthKit viene salvata.
3. La sessione salvata allenamenti, aggiungere qualsiasi esempi (ad esempio energia masterizzata o distanza).

### <a name="ending-the-session"></a>Terminare la sessione

Per terminare la sessione allenamenti, chiamare il `EndWorkoutSession` metodo il `HKHealthStore` passando il `HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

Verranno reimpostati i sensori di dispositivi per la modalità normale. Al termine HealthKit finale di allenamenti, riceverà un callback per la `DidChangeToState` metodo il `HKWorkoutSessionDelegate`:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>Salvataggio della sessione

Dopo l'applicazione ha terminato la sessione di allenamenti, sarà necessario creare accinge (`HKWorkout`) e salvarlo (insieme a un tipo di eventi) nell'archivio dati HealthKit (`HKHealthStore`):

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

Questo codice crea la quantità totale di energia masterizzata e distanza richiedere l'allenamenti come `HKQuantity` oggetti. Viene creato un dizionario di metadati che definiscono l'allenamenti e viene specificato il percorso dell'allenamenti:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Un nuovo `HKWorkout` oggetto viene creato con lo stesso `HKWorkoutActivityType` come il `HKWorkoutSession`, le date di iniziale e di fine, l'elenco degli eventi (viene accumulato dalle sezioni riportate sopra), l'energia masterizzata, totale distanza e il dizionario di metadati. Questo oggetto viene salvato nell'archivio integrità e gli eventuali errori gestiti.  

### <a name="adding-samples"></a>Aggiunta di esempi

Quando l'app Salva un set di esempi accinge, HealthKit genera una connessione tra gli esempi e allenamenti stesso in modo che l'app può eseguire una query HealthKit per tutti gli esempi associati accinge specificato in un secondo momento. Utilizza queste informazioni, l'applicazione può generare grafici dai dati allenamenti e li tracciato con una sequenza temporale allenamenti.

Per un'app contribuire a anello di spostare l'attività dell'app, deve includere gli esempi di energia con l'allenamenti salvato. Inoltre, i totali per distanza ed energia devono corrispondere la somma di tutti gli esempi che associa l'app con accinge salvato.

Per aggiungere esempi al accinge salvato, eseguire le operazioni seguenti:

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

Facoltativamente, l'applicazione può calcolare e creare un subset ridotto di campioni o di un campione mega (estensione l'intero intervallo di allenamenti) che quindi viene associato all'oggetto di allenamenti salvato.

## <a name="workouts-and-ios-10"></a>Allenamenti e iOS 10

Ogni app allenamenti watchOS 3 è un'app di base allenamenti iOS 10 padre, nuovi e a 10, iOS app iOS può essere utilizzata per avviare accinge che inserirà l'Apple Watch in modalità allenamenti (senza intervento dell'utente) ed eseguire l'app watchOS nella modalità di esecuzione in Background (vedere [su in esecuzione in Background](#About-Background-Running) sopra per altri dettagli).

Durante l'esecuzione di app watchOS WatchConnectivity può utilizzare per la comunicazione con l'app iOS padre e di messaggistica.

Esaminare il funzionamento di questo processo:

[ ![](workout-apps-images/workout06.png "iPhone e diagramma comunicazione Apple Watch")](workout-apps-images/workout06.png)

1. L'app iPhone crea un `HKWorkoutConfiguration` dell'oggetto e imposta il tipo di allenamenti e il percorso.
2. Il `HKWorkoutConfiguration` oggetto viene inviato la versione dell'app di Apple Watch e, se non è già in esecuzione, viene avviato dal sistema.
3. Utilizza il valore passato nella configurazione allenamenti, una nuova sessione allenamenti di avvio dell'app watchOS 3 (`HKWorkoutSession`).

> [!IMPORTANT]
> **Nota:** affinché l'app iPhone padre per l'avvio accinge nel Apple Watch, l'app watchOS 3 deve essere in Background in esecuzione abilitato. Vedere [attivazione in esecuzione in Background](#Enabling-Background-Running) sopra per altri dettagli.

Questo processo è molto simile al processo di avvio di una sessione di allenamenti nell'app watchOS 3 direttamente. In un iPhone, utilizzare il codice seguente:

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

Questo codice garantisce che è installata la versione watchOS dell'app e la versione di iPhone può connettersi ad essa prima:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

Quindi, viene creato un `HKWorkoutConfiguration` come di consueto e utilizza il `StartWatchApp` metodo il `HKHealthStore` per inviarlo per l'Apple Watch e avviare l'app e la sessione allenamenti.

E sull'app di espressioni di controllo del sistema operativo, utilizzare il codice seguente nel `WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Accetta il `HKWorkoutConfiguration` e crea un nuovo `HKWorkoutSession` e lo collega a un'istanza della classe personalizzata `HKWorkoutSessionDelegate`. È stata avviata la sessione allenamenti dell'archivio integrità HealthKit dell'utente.

## <a name="bringing-all-the-pieces-together"></a>Unione di tutte le parti

Richiede tutte le informazioni contenute nel presente documento, un'app di base allenamenti watchOS 3 e app allenamenti in base 10 di iOS padre potrebbe includere le parti seguenti:

1. **iOS 10 `ViewController.cs`**  -gestisce l'avvio di una sessione di connettività di espressioni di controllo e accinge sul Apple Watch.
2. **watchOS 3 `ExtensionDelegate.cs`**  -gestisce la versione watchOS 3 dell'app allenamenti.
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -personalizzato `HKWorkoutSessionDelegate` per gestire gli eventi per l'allenamenti.

> [!IMPORTANT]
> **Nota:** il codice illustrato nelle sezioni seguenti sono incluse solo le parti necessarie per implementare le funzionalità nuove e migliorate fornite alle App allenamenti in watchOS 3. Tutto il codice di supporto e il codice per presentare e aggiornare l'interfaccia utente non è inclusi, ma possono essere creati facilmente seguendo a altra documentazione watchOS.<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

Il `ViewController.cs` file nella versione di iOS 10 padre dell'app allenamenti includerà il codice seguente:

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

Il `ExtensionDelegate.cs` file nella versione dell'app allenamenti watchOS 3 include il codice seguente:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

Il `OutdoorRunDelegate.cs` file nella versione dell'app allenamenti watchOS 3 include il codice seguente:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>Suggerimenti

Apple consiglia di usare le seguenti procedure consigliate durante la progettazione e implementazione allenamenti App watchOS 3 e iOS 10:

- Verificare che l'app allenamenti watchOS 3 sia funziona anche quando non è in grado di connettersi a iPhone e la versione di iOS 10 dell'app.
- Usare HealthKit distanza quando GPS è disponibile perché è in grado di generare gli esempi di distanza senza GPS.
- Consentire all'utente di avviare l'allenamenti da di Apple Watch o iPhone.
- Consenti all'app da visualizzare allenamenti da altre origini (ad esempio altre applicazioni di terze parti 3rd) nelle relative visualizzazioni di dati cronologici.
- Assicurarsi che l'app non visualizzato eliminato allenamenti nei dati cronologici.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i miglioramenti Apple ha apportate alle App allenamenti watchOS 3 e come implementarli in Xamarin.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introduzione a HealthKit](~/ios/platform/healthkit.md)
