---
title: le App per gli allenamenti in Xamarin watchOS
description: Questo articolo illustra i miglioramenti di Apple ha apportato alle App per gli allenamenti in watchOS 3 e come implementarli in Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 02db7dce6ba38b6c1e943ff189ff69efb7cc1c08
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677833"
---
# <a name="watchos-workout-apps-in-xamarin"></a>le App per gli allenamenti in Xamarin watchOS

_Questo articolo illustra i miglioramenti di Apple ha apportato alle App per gli allenamenti in watchOS 3 e come implementarli in Xamarin._


Nuovo per watchOS 3, per gli allenamenti correlati le app hanno la possibilità di eseguire in background nel Apple Watch e accedere ai dati di HealthKit. Le app di iOS 10 basato su padre ha anche la possibilità di avviare l'app watchOS 3 basato senza intervento dell'utente.

Gli argomenti seguenti vengono descritti nel dettaglio:

## <a name="about-workout-apps"></a>Sulle App per gli allenamenti

Gli utenti delle App per gli allenamenti e adeguatezza possono essere altamente dedicati, dedicare diverse ore del giorno verso i propri obiettivi di integrità e di adeguatezza. Di conseguenza, si aspettano App reattiva e facile da usare in modo accurato raccogliere e visualizzare i dati e si integrano facilmente con integrità di Apple.

Un'app di idoneità o per gli allenamenti ben progettata consente agli utenti le attività per raggiungere gli obiettivi di idoneità del grafico. Tramite l'Apple Watch, le app di idoneità e per gli allenamenti hanno accesso immediato a cuore frequenza, calorie velocità e attività di rilevamento.

[![](workout-apps-images/workout01.png "Esempio di app di idoneità e per gli allenamenti")](workout-apps-images/workout01.png#lightbox)

Nuovo a watchOS 3, _in esecuzione in Background_ allenamento offre correlati App la possibilità di eseguire in background nel Apple Watch e accedere ai dati di HealthKit.

Questo documento è stata introdotta la funzionalità in esecuzione in Background, illustrano il ciclo di vita per gli allenamenti e Mostra come un'app per gli allenamenti può favorire il suo _anelli attività_ sul Apple Watch.

## <a name="about-workout-sessions"></a>Informazioni sulle sessioni per gli allenamenti

Il nucleo di ogni app per gli allenamenti è un _sessione per gli allenamenti_ (`HKWorkoutSession`) che l'utente può avviare e arrestare. L'API di sessione per gli allenamenti è facile da implementare e offre diversi vantaggi a un'app per gli allenamenti, ad esempio:

- Lo spostamento e calorie masterizzare il rilevamento in base al tipo di attività.
- Automatico contributo per gli anelli di attività dell'utente.
- Mentre in una sessione, l'app verrà visualizzata automaticamente quando l'utente si attiva il dispositivo (in base alla loro polso generazione o l'interazione con l'Apple Watch).

## <a name="about-background-running"></a>Sull'esecuzione in Background

Come indicato in precedenza, con watchOS 3 è possibile impostare un'app per gli allenamenti per l'esecuzione in background. Usando l'app per gli allenamenti in esecuzione in Background può elaborare i dati dei sensori dell'Apple Watch durante l'esecuzione in background. Ad esempio, un'app può continuare a monitorare velocità cuore dell'utente, anche se non viene più visualizzato nella schermata.

In esecuzione in background offre anche la possibilità di presentare all'utente feedback in tempo reale in qualsiasi momento durante una sessione attiva per gli allenamenti, ad esempio l'invio di un avviso aptico per informare l'utente di avanzamento corrente.

Inoltre, in esecuzione in Background consente all'app di aggiornare rapidamente l'interfaccia utente in modo che l'utente ha i dati più recenti quando essi rapidamente visualizzare rapidamente i Apple Watch.

Per mantenere prestazioni elevate su Apple Watch, un'app usando in esecuzione in Background debba limitare la quantità di attività in background per conservare la batteria. Se un'app utilizza troppe risorse della CPU mentre è in background, possono essere sospesi per watchOS.

### <a name="enabling-background-running"></a>Attivazione in Background in esecuzione

Per abilitare l'esecuzione in Background, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic dell'app iPhone complementare di espressioni di controllo dell'estensione `Info.plist` file per aprirlo e modificarlo.
2. Passare al **origine** Vista: 

    [![](workout-apps-images/plist01.png "La visualizzazione origine")](workout-apps-images/plist01.png#lightbox)
3. Aggiungere una nuova chiave denominata `WKBackgroundModes` e impostare il **tipo** a `Array`: 

    [![](workout-apps-images/plist02.png "Aggiungere una nuova chiave denominata WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Aggiungere un nuovo elemento nella matrice con il **tipo** dei `String` e il valore `workout-processing`: 

    [![](workout-apps-images/plist03.png "Aggiungere un nuovo elemento nella matrice con il tipo di stringa e il valore per l'elaborazione per gli allenamenti")](workout-apps-images/plist03.png#lightbox)
5. Salvare le modifiche apportate al file.

## <a name="starting-a-workout-session"></a>Avviare una sessione per gli allenamenti

Esistono tre passaggi principali per l'avvio di una sessione per gli allenamenti:

[![](workout-apps-images/workout02.png "I tre passaggi principali per avviare una sessione per gli allenamenti")](workout-apps-images/workout02.png#lightbox)

1. L'app deve richiedere l'autorizzazione per accedere ai dati in HealthKit.
2. Creare un oggetto di configurazione per gli allenamenti per il tipo di allenamenti in corso l'avvio.
3. Creare e avviare una sessione per gli allenamenti tramite la configurazione per gli allenamenti appena creata.

### <a name="requesting-authorization"></a>La richiesta di autorizzazione

Prima di un'app può accedere a dati HealthKit dell'utente, deve richiedere e autorizzazione alla ricezione da parte dell'utente. A seconda della natura dell'app per gli allenamenti potrebbe avere i seguenti tipi di richieste:

- Autorizzazione per scrivere i dati:
    - Allenamenti
- Autorizzazione alla lettura dei dati:
    - Energia attualmente consumata
    - distanza
    - Tariffa di base  

Prima di un'app può richiedere l'autorizzazione, deve essere configurato per accedere a HealthKit.

Seguire questa procedura:

1. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
2. Scorrere verso il basso e selezionare **Abilita HealthKit**: 

    [![](workout-apps-images/auth01.png "Controllo Abilita HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Salvare le modifiche apportate al file.
4. Seguire le istruzioni nel [ID App esplicito e il profilo di Provisioning](~/ios/platform/healthkit.md) e [associando l'ID App e il Provisioning di profilo con l'App di xamarin. IOS](~/ios/platform/healthkit.md) sezioni del [Introduzione a HealthKit](~/ios/platform/healthkit.md) articolo effettuare correttamente il provisioning dell'app.
5. Infine, usare le istruzioni riportate nel [programmazione integrità Kit](~/ios/platform/healthkit.md) e [che richiede l'autorizzazione dall'utente](~/ios/platform/healthkit.md) sezioni del [Introduzione a HealthKit](~/ios/platform/healthkit.md) articolo a richiesta autorizzazione per l'accesso archivio dati di HealthKit dell'utente.

### <a name="setting-the-workout-configuration"></a>Impostazione della configurazione per gli allenamenti

Per gli allenamenti sessioni vengono create utilizzando un oggetto di configurazione per gli allenamenti (`HKWorkoutConfiguration`) che specifica il tipo per gli allenamenti (ad esempio `HKWorkoutActivityType.Running`) e il percorso per gli allenamenti (ad esempio `HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Creazione di un delegato di sessione per gli allenamenti 

Per gestire gli eventi che possono verificarsi durante una sessione per gli allenamenti, l'app sarà necessario creare un'istanza del delegato di sessione per gli allenamenti. Aggiungere una nuova classe al progetto e associarla all'esterno del `HKWorkoutSessionDelegate` classe. Per un esempio di un'esecuzione all'aperto, può avere un aspetto simile al seguente:

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

Questa classe crea diversi eventi che verranno generati lo stato delle modifiche per gli allenamenti sessione (`DidChangeToState`) e se ha esito negativo della sessione per gli allenamenti (`DidFail`). 

### <a name="creating-a-workout-session"></a>Creazione di una sessione per gli allenamenti

Utilizzando la configurazione di allenamenti e per gli allenamenti sessione delegato creato in precedenza per creare una nuova sessione per gli allenamenti e avviarla su archivio HealthKit predefinito dell'utente:

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

Se l'app viene avviata la sessione per gli allenamenti e l'utente passa nuovamente alla loro quadrante dell'orologio, verrà visualizzata una piccolo icona verde "in esecuzione man" sopra l'immagine:

[![](workout-apps-images/workout03.png "Un'icona di uomo in esecuzione verde piccolo, visualizzata sopra il viso")](workout-apps-images/workout03.png#lightbox)

Se l'utente tocca l'icona, verrà reindirizzato nuovamente all'app.

## <a name="data-collection-and-control"></a>Raccolta dei dati e controllo

Dopo che è stata configurata e avviata una sessione per gli allenamenti, l'app dovrà raccogliere i dati relativi alla sessione (ad esempio frequenza cuore dell'utente) e controllare lo stato della sessione:

[![](workout-apps-images/workout04.png "Diagramma di controllo e la raccolta dei dati")](workout-apps-images/workout04.png#lightbox)

1. **Osservando esempi** -l'app sarà necessario recuperare informazioni dall'HealthKit che verranno elaborati e visualizzati all'utente.
2. **Osservando gli eventi** -l'app deve rispondere agli eventi generati da HealthKit o dall'interfaccia utente dell'app (ad esempio, l'utente la sospensione di allenamenti).
3. **Immettere lo stato di esecuzione** -sessione è stata avviata ed è attualmente in esecuzione.
4. **Entrare in stato sospeso** -l'utente ha sospeso la sessione corrente per gli allenamenti e possibile riavviarla in un secondo momento. L'utente può passare tra gli stati di sospensione e in esecuzione più volte in una singola sessione per gli allenamenti.
5. **Terminare la sessione per gli allenamenti** : in qualsiasi momento l'utente può terminare la sessione per gli allenamenti o può scadere e terminare in modo autonomo se fosse a consumo accinge (ad esempio, un'esecuzione di due miglia).

Il passaggio finale consiste per salvare i risultati della sessione per gli allenamenti all'archivio dati di HealthKit dell'utente.

### <a name="observing-healthkit-samples"></a>Osservando esempi di HealthKit

L'app sarà necessario aprire una _Query di oggetto di ancoraggio_ per ognuno dei dati HealthKit punti è interessato, ad esempio frequenza cuore o sull'energia active masterizzata. Per ogni punto dati osservato, un gestore degli aggiornamenti dovrà essere creato per acquisire nuovi dati quando vengono inviata all'app.

Da questi punti dati, l'app può accumulare i totali (ad esempio la distanza di esecuzione totale) e relativa interfaccia utente aggiornata in base alle esigenze. Inoltre, l'app può inviare una notifica agli utenti quando hanno raggiunto un obiettivo specifico o il conseguimento, ad esempio il completamento di miglia successivo di un'esecuzione.

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

Crea un predicato per impostare la data inizia a cui si vuole ottenere i dati per l'uso di `GetPredicateForSamples` (metodo). Crea un set di dispositivi per estrarre le informazioni di HealthKit da tramite il `GetPredicateForObjectsFromDevices` metodo, in questo caso l'Apple Watch locale solo (`HKDevice.LocalDevice`). I due predicati vengono combinati in un predicato composta (`NSCompoundPredicate`) usando il `CreateAndPredicate` (metodo).

Una nuova `HKAnchoredObjectQuery` viene creato per il punto di dati desiderato (in questo caso `HKQuantityTypeIdentifier.ActiveEnergyBurned` per il punto dati masterizzata energia attivi), viene imposto alcun limite sulla quantità di dati restituiti (`HKSampleQuery.NoLimit`) e viene definito un gestore di aggiornamento per la gestione in corso di dati restituito all'app da HealthKit. 

Il gestore di aggiornamento verrà chiamato ogni volta che i nuovi dati viene recapitati all'app per il punto dati specificato. Se viene restituito alcun errore, l'app può in modo sicuro la lettura dei dati, verificare tutti i calcoli necessari e aggiornamento dell'interfaccia utente in base alle esigenze.

Il codice esegue il ciclo attraverso tutti gli esempi (`HKSample`) restituiti nel `addedObjects` della matrice e li esegue il cast a un campione di quantità (`HKQuantitySample`). Viene quindi ottenuto il valore double dell'esempio di come un joule (`HKUnit.Joule`) e lo accumula nel totale parziale dell'active energia attualmente consumata per l'allenamento e aggiorna l'interfaccia utente.

### <a name="achieved-goal-notification"></a>Notifica di obiettivo raggiunto

Come accennato in precedenza, quando l'utente raggiunge un obiettivo dell'App per gli allenamenti (ad esempio completato il miglio prima di un'esecuzione), può inviare feedback aptico all'utente tramite il motore Taptic. L'app deve aggiornare anche relativa interfaccia utente a questo punto, poiché l'utente genererà molto probabilmente il polso per visualizzare l'evento che viene richiesto il feedback.

Per riprodurre il feedback aptico, usare il codice seguente:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Osservando gli eventi

Gli eventi sono timestamp che l'app può usare per evidenziare alcuni punti durante allenamento dell'utente. Alcuni eventi verranno creati direttamente dall'app e salvati nell'allenamento e alcuni eventi verranno create automaticamente da HealthKit.

Per osservare gli eventi creati da HealthKit, l'app eseguirà l'override di `DidGenerateEvent` metodo di `HKWorkoutSessionDelegate`:

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

Apple ha aggiunto i seguenti nuovi tipi di evento in watchOS 3:

- `HKWorkoutEventType.Lap` -Sono per gli eventi che suddivide l'allenamento in parti equidistanti. Ad esempio, per contrassegnare una visione generale di una traccia mentre è in esecuzione.
- `HKWorkoutEventType.Marker` -Sono per i punti arbitrari di interesse all'interno di allenamenti. Ad esempio, per raggiungere un punto specifico nella route di un'esecuzione all'aperto.

Questi nuovi tipi possono essere creati dall'app e archiviati nell'allenamento per un uso successivo nella creazione di grafici e le statistiche.

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

Questo codice crea una nuova istanza di un evento di marcatore (`HKWorkoutEvent`) e lo salva in una raccolta privata di eventi (che verranno successivamente scritti nella sessione per gli allenamenti) e informa l'utente dell'evento tramite haptics.

### <a name="pausing-and-resuming-workouts"></a>Sospensione e ripresa allenamenti

In qualsiasi punto in una sessione per gli allenamenti, l'utente può temporaneamente sospendere l'allenamento e ripristinarla in un secondo momento. Ad esempio, si potrebbe sospendere un'esecuzione in ambienti interni per richiedere un aspetto importante e riprendere l'esecuzione dopo la chiamata è stata completata.

Interfaccia utente dell'app deve fornire un modo per sospendere e riprendere l'allenamento (richiamando HealthKit) in modo che l'Apple Watch può risparmiare spazio power sia i dati mentre l'utente ha sospeso la loro attività. Inoltre, l'app deve ignorare eventuali nuovi punti dati che possono essere ricevuti durante la sessione per gli allenamenti è in uno stato di sospensione.

HealthKit rispondere alla richiesta per sospendere e riprendere le chiamate tramite la generazione di eventi di sospensione e ripresa. Mentre è in pausa la sessione per gli allenamenti, nessun nuovi eventi o dati verranno inviati all'app da HealthKit fino a quando la sessione viene ripresa.

Sospendere e riprendere una sessione per gli allenamenti, usare il codice seguente:

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

Gli eventi di sospensione e ripresa che verranno generati dal HealthKit gestibili eseguendo l'override di `DidGenerateEvent` metodo del `HKWorkoutSessionDelegate`:

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

Anche nuovi a watchOS 3, viene messo in pausa il movimento (`HKWorkoutEventType.MotionPaused`) e la ripresa di movimento (`HKWorkoutEventType.MotionResumed`) gli eventi. Questi eventi vengono generati automaticamente da HealthKit durante accinge in esecuzione quando l'utente avvia e interrompe lo spostamento.

Quando l'app riceve un evento di movimento in pausa, è necessario arrestare la raccolta dei dati fino a quando l'utente viene riattivato movimento e viene ricevuto l'evento movimento riprende. App non devono sospendere la sessione per gli allenamenti in risposta a un evento sospeso movimento.

> [!IMPORTANT]
> Gli eventi di movimento sospesa e ripresa di movimento sono supportati solo per il tipo di attività RunningWorkout (`HKWorkoutActivityType.Running`).

Anche in questo caso, questi eventi possono essere gestiti mediante l'override di `DidGenerateEvent` metodo del `HKWorkoutSessionDelegate`:

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

## <a name="ending-and-saving-the-workout-session"></a>Termina e il salvataggio della sessione per gli allenamenti

Quando l'utente ha completato le allenamenti, l'app sarà necessario terminare la sessione corrente per gli allenamenti e salvarlo nel database di HealthKit. Allenamenti salvati HealthKit verranno automaticamente visualizzati nell'elenco di attività per gli allenamenti.

Nuovo ai dispositivi iOS 10, incluso l'elenco di elenco delle attività per gli allenamenti anche iPhone dell'utente. In modo che anche se l'Apple Watch non è nelle vicinanze, verrà visualizzata l'allenamento sul telefono.

Allenamenti che includono i campioni di energia aggiornerà anello spostare dell'utente nell'app per le attività in modo che l'app di terze parti 3rd ora è possibile contribuire agli obiettivi spostamento giornalieri dell'utente.

Per terminare e salvare una sessione per gli allenamenti, sono necessari i passaggi seguenti:

[![](workout-apps-images/workout05.png "Termina e il salvataggio del diagramma di sessione per gli allenamenti")](workout-apps-images/workout05.png#lightbox)

1. L'app in primo luogo, sarà necessario terminare la sessione per gli allenamenti.
2. La sessione per gli allenamenti viene salvata HealthKit.
3. Aggiungere eventuali esempi (ad esempio distanza o energia attualmente consumata) la sessione per gli allenamenti salvata.

### <a name="ending-the-session"></a>Arresto della sessione

Per terminare la sessione per gli allenamenti, chiamare il `EndWorkoutSession` metodo per il `HKHealthStore` passando la `HKWorkoutSession`:

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

Questa operazione ripristina i sensori di dispositivi per le modalità normale. Al termine HealthKit che termina l'allenamenti, riceverà un callback per il `DidChangeToState` metodo del `HKWorkoutSessionDelegate`:

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

Una volta che l'app ha terminato la sessione per gli allenamenti, sarà necessario creare accinge (`HKWorkout`) e salvarlo (insieme a un tipo di eventi) all'archivio dati HealthKit (`HKHealthStore`):

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

Questo codice crea il richiedono quantità totale di energia attualmente consumata e distanza per l'allenamento come `HKQuantity` oggetti. Viene creato un dizionario di metadati che definiscono l'allenamento e viene specificato il percorso delle per gli allenamenti:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Una nuova `HKWorkout` oggetto viene creato con lo stesso `HKWorkoutActivityType` come la `HKWorkoutSession`, le date di inizio e fine, l'elenco degli eventi (da accumulati da nelle sezioni precedenti), l'energia attualmente consumata, complessivo distanza e il dizionario di metadati. Questo oggetto viene salvato in Store l'integrità e gli eventuali errori gestiti.  

### <a name="adding-samples"></a>Aggiunta di esempi

Quando l'app Salva un set di esempi accinge, HealthKit genera una connessione tra gli esempi e Allenamento stesso in modo che l'app può eseguire una query HealthKit in un secondo momento per tutti gli esempi associati accinge specificato. Utilizzando queste informazioni, l'app può generare grafici dai dati per gli allenamenti e traccia in una sequenza temporale per gli allenamenti.

Per un'app contribuire al Ring spostare dell'app per le attività, deve includere gli esempi di energia con l'allenamento salvato. Inoltre, i totali per distanza ed energia devono corrispondere la somma degli eventuali esempi che associa l'app a accinge salvato.

Per aggiungere esempi accinge salvato, eseguire le operazioni seguenti:

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

Facoltativamente, l'app può calcolare e creare un subset ridotto di campioni o un campione di mega (che si estende su tutto l'intervallo delle per gli allenamenti) che viene quindi associato con l'allenamento salvato.

## <a name="workouts-and-ios-10"></a>Allenamenti e iOS 10

Ogni app allenamenti watchOS 3 è un'app di base allenamenti iOS 10 padre, nuovi e a 10, iOS app iOS può essere utilizzata per avviare accinge che inserirà l'Apple Watch in modalità allenamenti (senza intervento dell'utente) ed eseguire l'app watchOS nella modalità di esecuzione in Background (vedere [su in esecuzione in Background](#about-background-running) sopra per altri dettagli).

Mentre è in esecuzione l'app watchOS, WatchConnectivity può usare per la messaggistica e comunicazione con l'app per iOS padre.

Ottenere un quadro di come funziona questo processo:

[![](workout-apps-images/workout06.png "diagramma di comunicazione di Apple Watch e iPhone")](workout-apps-images/workout06.png#lightbox)

1. L'app iPhone crea un `HKWorkoutConfiguration` dell'oggetto e imposta il tipo per gli allenamenti e il percorso.
2. Il `HKWorkoutConfiguration` oggetto viene inviato la versione di Apple Watch dell'app e, se non è già in esecuzione, viene avviato dal sistema.
3. Utilizzando i valori passati nella configurazione per gli allenamenti, l'app watchOS 3 viene avviata una nuova sessione per gli allenamenti (`HKWorkoutSession`).

> [!IMPORTANT]
> Affinché l'app iPhone padre per l'avvio accinge nel Apple Watch, l'app watchOS 3 deve essere in esecuzione in Background abilitata. Vedi [abilitazione in esecuzione in Background](#enabling-background-running) sopra per altri dettagli.

Questo processo è molto simile al processo di avvio di una sessione per gli allenamenti nell'app watchOS 3 direttamente. In un iPhone, usare il codice seguente:

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

Questo codice garantisce che sia installata la versione di watchOS dell'app e la versione di iPhone possa connettersi a essa prima di tutto:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

Quindi, viene creato un `HKWorkoutConfiguration` come di consueto e utilizza il `StartWatchApp` metodo il `HKHealthStore` inviarlo per l'Apple Watch e avviare l'app e la sessione per gli allenamenti.

E App watch OS, usare il codice seguente nel `WKExtensionDelegate`:

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

Richiede la `HKWorkoutConfiguration` e crea un nuovo `HKWorkoutSession` e la collega un'istanza della classe personalizzata `HKWorkoutSessionDelegate`. La sessione per gli allenamenti è stata avviata HealthKit Health Store il suo.

## <a name="bringing-all-the-pieces-together"></a>Riunisce tutte le parti

Richiedendo tutte le informazioni presentate in questo documento, un'app di base per gli allenamenti watchOS 3 e l'app di base per gli allenamenti iOS 10 padre possono includere le parti seguenti:

1. **iOS 10 `ViewController.cs`**  -gestisce l'avvio di una sessione di espressioni di controllo della connettività e un per gli allenamenti sul Apple Watch.
2. **watchOS 3 `ExtensionDelegate.cs`**  -gestisce la versione di watchOS 3 dell'app per gli allenamenti.
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -personalizzato `HKWorkoutSessionDelegate` per gestire gli eventi per l'allenamenti.

> [!IMPORTANT]
> Il codice illustrato nelle sezioni seguenti include solo le parti necessarie per implementare le funzionalità nuove e migliorate fornite per le App per gli allenamenti in watchOS 3. Tutto il codice di supporto e il codice per presentare e aggiornare l'interfaccia utente non è inclusi, ma possono essere creati facilmente attenendosi a altra documentazione watchOS.<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

Il `ViewController.cs` file nella versione di iOS 10 padre dell'app per gli allenamenti includerà il codice seguente:

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

Il `ExtensionDelegate.cs` file nella versione dell'app per gli allenamenti watchOS 3 includerà il codice seguente:

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

Il `OutdoorRunDelegate.cs` file nella versione dell'app per gli allenamenti watchOS 3 includerà il codice seguente:

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

Usando le procedure consigliate seguenti durante la progettazione e implementazione di App per gli allenamenti in iOS 10 e watchOS 3 suggerite da Apple:

- Assicurarsi che l'app per gli allenamenti watchOS 3 sia funziona anche quando non è in grado di connettersi a iPhone e la versione di iOS 10 dell'app.
- Usare distanza HealthKit quando GPS non è disponibile perché è in grado di generare gli esempi di distanza senza GPS.
- Consentire all'utente di avviare l'allenamento dal Apple Watch o iPhone.
- Consentire all'app per la visualizzazione allenamenti da altre origini (ad esempio ad altre app di terze parti 3rd) nelle visualizzazioni dei dati cronologici.
- Assicurarsi che l'app non visualizzato eliminato allenamenti nei dati cronologici.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti di Apple ha apportato alle App per gli allenamenti in watchOS 3 e come implementarli in Xamarin.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introduzione a HealthKit](~/ios/platform/healthkit.md)
