---
title: >
  App di allenamento watchos in Xamarin
description: >-
  Questo articolo descrive i miglioramenti apportati da Apple alle app di allenamento in watchos 3 e come implementarli in Xamarin

  .
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 0b1827d8936343cbf977395a788a466f1c3f60ac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032746"
---
# <a name="watchos-workout-apps-in-xamarin"></a>App di allenamento watchos in Xamarin


_Questo articolo descrive i miglioramenti apportati da Apple alle app di allenamento in watchos 3 e come implementarli in Xamarin
._

Una novità di watchos 3, le app correlate all'allenamento hanno la possibilità di essere eseguite in background nell'Apple Watch e di accedere ai dati HealthKit. L'app basata su iOS 10 padre ha anche la possibilità di avviare l'app watchos 3 basata senza l'intervento dell'utente.

Gli argomenti seguenti vengono descritti nel dettaglio:

## <a name="about-workout-apps"></a>Informazioni sulle app di allenamento

Gli utenti delle app per fitness e allenamento possono essere altamente dedicati e dedicare diverse ore del giorno ai propri obiettivi di integrità e di idoneità. Di conseguenza, si aspettano app reattive e facili da usare che raccolgono e visualizzano i dati in modo accurato e si integrano perfettamente con l'integrità di Apple.

Un'app per fitness o allenamento ben progettata consente agli utenti di tracciare le proprie attività per raggiungere gli obiettivi di idoneità. Usando le app di Apple Watch, fitness e allenamento hanno accesso immediato a frequenza cardiaca, ustioni calorie e rilevamento attività.

[![](workout-apps-images/workout01.png "Fitness and workout app example")](workout-apps-images/workout01.png#lightbox)

Una novità di watchos 3, l' _esecuzione in background_ fornisce alle app correlate all'allenamento la possibilità di eseguire in background sul Apple Watch e di accedere ai dati HealthKit.

Questo documento introduce la funzionalità di esecuzione in background, illustra il ciclo di vita dell'app di allenamento e Mostra come un'app di allenamento può contribuire agli _anelli di attività_ dell'utente nel Apple Watch.

## <a name="about-workout-sessions"></a>Informazioni sulle sessioni di allenamento

Il fulcro di ogni app di allenamento è una _sessione di allenamento_ (`HKWorkoutSession`) che l'utente può avviare e arrestare. L'API della sessione di allenamento è facile da implementare e offre diversi vantaggi a un'app di allenamento, ad esempio:

- Rilevamento di movimenti e calorie in base al tipo di attività.
- Contributo automatico agli anelli attività dell'utente.
- Durante una sessione, l'app verrà visualizzata automaticamente ogni volta che l'utente riattiva il dispositivo (aumentando il polso o interagendo con il Apple Watch).

## <a name="about-background-running"></a>Informazioni sull'esecuzione in background

Come indicato in precedenza, con watchos 3 un'app di allenamento può essere impostata in modo da essere eseguita in background. Con l'esecuzione in background di un'app di allenamento è possibile elaborare i dati dai sensori del Apple Watch durante l'esecuzione in background. Ad esempio, un'app può continuare a monitorare la frequenza cardiaca dell'utente, anche se non è più visualizzata sullo schermo.

L'esecuzione in background consente inoltre di presentare feedback in tempo reale all'utente in qualsiasi momento durante una sessione di allenamento attiva, ad esempio l'invio di un avviso tattile per informare l'utente dello stato di avanzamento corrente.

Inoltre, l'esecuzione in background consente all'app di aggiornare rapidamente l'interfaccia utente, in modo che l'utente disponga dei dati più recenti quando esamina rapidamente la Apple Watch.

Per garantire prestazioni elevate in Apple Watch, un'app Watch che usa l'esecuzione in background dovrebbe limitare la quantità di lavoro in background per conservare la batteria. Se un'app usa una CPU eccessiva in background, può essere sospesa da watchos.

### <a name="enabling-background-running"></a>Abilitazione dell'esecuzione in background

Per abilitare l'esecuzione in background, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**fare doppio clic sul file di `Info.plist` dell'app per l'estensione Watch per aprirlo per la modifica.
2. Passare alla visualizzazione **origine** : 

    [![](workout-apps-images/plist01.png "The Source view")](workout-apps-images/plist01.png#lightbox)
3. Aggiungere una nuova chiave denominata `WKBackgroundModes` e impostare il **tipo** su `Array`: 

    [![](workout-apps-images/plist02.png "Add a new key called WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Aggiungere un nuovo elemento alla matrice con il **tipo** di `String` e il valore `workout-processing`: 

    [![](workout-apps-images/plist03.png "Add a new item to the array with the Type of String and a value of workout-processing")](workout-apps-images/plist03.png#lightbox)
5. Salvare le modifiche apportate al file.

## <a name="starting-a-workout-session"></a>Avvio di una sessione di allenamento

L'avvio di una sessione di allenamento prevede tre passaggi principali:

[![](workout-apps-images/workout02.png "The three main steps to starting a Workout Session")](workout-apps-images/workout02.png#lightbox)

1. L'app deve richiedere l'autorizzazione per accedere ai dati in HealthKit.
2. Creare un oggetto di configurazione dell'allenamento per il tipo di allenamento avviato.
3. Creare e avviare una sessione di allenamento usando la configurazione di allenamento appena creata.

### <a name="requesting-authorization"></a>Richiesta dell'autorizzazione

Prima che un'app possa accedere ai dati HealthKit dell'utente, deve richiedere e ricevere l'autorizzazione da parte dell'utente. A seconda della natura dell'app di allenamento, potrebbe effettuare i tipi di richieste seguenti:

- Autorizzazione per la scrittura dei dati:
  - Allenamenti
- Autorizzazione per la lettura dei dati:
  - Energia bruciata
  - Distanza
  - Frequenza cardiaca  

Prima che un'app possa richiedere l'autorizzazione, è necessario configurarla per accedere a HealthKit.

Procedere come descritto di seguito:

1. In **Esplora soluzioni** fare doppio clic sul file `Entitlements.plist` per aprirlo e modificarlo.
2. Scorrere fino alla fine e selezionare **Enable HealthKit**: 

    [![](workout-apps-images/auth01.png "Check Enable HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Salvare le modifiche apportate al file.
4. Seguire le istruzioni riportate nell'articolo relativo all' [ID app esplicito e al profilo di provisioning](~/ios/platform/healthkit.md) e [associando l'ID app e il profilo di provisioning all'app Xamarin.iOS](~/ios/platform/healthkit.md) dell'articolo [Introduzione a HealthKit](~/ios/platform/healthkit.md) per eseguire correttamente il provisioning dell'app.
5. Infine, usare le istruzioni disponibili in [Programming Health Kit](~/ios/platform/healthkit.md) e [richiedere l'autorizzazione dalle sezioni utente](~/ios/platform/healthkit.md) dell'articolo [Introduzione a HealthKit](~/ios/platform/healthkit.md) per richiedere l'autorizzazione per l'accesso all'archivio dati HealthKit dell'utente.

### <a name="setting-the-workout-configuration"></a>Impostazione della configurazione di allenamento

Le sessioni di allenamento vengono create usando un oggetto di configurazione dell'allenamento (`HKWorkoutConfiguration`) che specifica il tipo di allenamento, ad esempio `HKWorkoutActivityType.Running`, e la posizione dell'allenamento, ad esempio `HKWorkoutSessionLocationType.Outdoor`:

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
  ActivityType = HKWorkoutActivityType.Running,
  LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Creazione di un delegato della sessione di allenamento 

Per gestire gli eventi che possono verificarsi durante una sessione di allenamento, l'app deve creare un'istanza del delegato della sessione di allenamento. Aggiungere una nuova classe al progetto e basarla sulla classe `HKWorkoutSessionDelegate`. Per l'esempio di un'esecuzione esterna, potrebbe essere simile alla seguente:

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

Questa classe crea diversi eventi che verranno generati quando lo stato della sessione di allenamento cambia (`DidChangeToState`) e se la sessione di allenamento ha esito negativo (`DidFail`). 

### <a name="creating-a-workout-session"></a>Creazione di una sessione di allenamento

Usando la configurazione di allenamento e il delegato della sessione di allenamento creato in precedenza per creare una nuova sessione di allenamento e avviarla con l'archivio HealthKit predefinito dell'utente:

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

Se l'app avvia questa sessione di allenamento e l'utente torna al quadrante dell'orologio, viene visualizzata una piccola icona "Running Man" verde sopra la faccia:

[![](workout-apps-images/workout03.png "A tiny green running man icon displayed above the face")](workout-apps-images/workout03.png#lightbox)

Se l'utente tocca questa icona, verranno riportate all'app.

## <a name="data-collection-and-control"></a>Raccolta e controllo dei dati

Una volta configurata e avviata una sessione di allenamento, l'app dovrà raccogliere dati sulla sessione (ad esempio la frequenza cardiaca dell'utente) e controllare lo stato della sessione:

[![](workout-apps-images/workout04.png "Data Collection and Control Diagram")](workout-apps-images/workout04.png#lightbox)

1. **Osservazione degli esempi** : l'app dovrà recuperare le informazioni da HealthKit che verranno eseguite e visualizzate all'utente.
2. **Osservazione degli eventi** : l'app dovrà rispondere agli eventi generati da HealthKit o dall'interfaccia utente dell'app, ad esempio l'utente che sospende l'allenamento.
3. **Immettere lo stato di esecuzione** : la sessione è stata avviata ed è attualmente in esecuzione.
4. **Immettere lo stato sospeso** : l'utente ha sospeso la sessione di allenamento corrente e può riavviarla in un secondo momento. L'utente può passare più volte tra gli stati in esecuzione e in pausa in un'unica sessione di allenamento.
5. **Terminare la sessione di allenamento** : in qualsiasi momento l'utente può terminare la sessione di allenamento oppure scadere e terminare autonomamente se si tratta di un allenamento a consumo (ad esempio, un'esecuzione a due miglia).

Il passaggio finale consiste nel salvare i risultati della sessione di allenamento nell'archivio dati HealthKit dell'utente.

### <a name="observing-healthkit-samples"></a>Osservare gli esempi di HealthKit

L'app dovrà aprire una query di _oggetto di ancoraggio_ per ognuno dei punti dati HealthKit a cui è interessato, ad esempio la frequenza cardiaca o l'energia attiva bruciata. Per ogni punto dati osservato, è necessario creare un gestore di aggiornamento per acquisire i nuovi dati quando vengono inviati all'app.

Da questi punti dati, l'app può accumulare totali, ad esempio la distanza totale di esecuzione, e aggiornare l'interfaccia utente in base alle esigenze. Inoltre, l'app può notificare agli utenti che hanno raggiunto un obiettivo o un raggiungimento specifico, ad esempio il completamento del miglio successivo di un'esecuzione.

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

Viene creato un predicato per impostare la data di inizio per cui si desidera ottenere i dati utilizzando il metodo `GetPredicateForSamples`. Viene creato un set di dispositivi per estrarre le informazioni HealthKit dall'uso del metodo `GetPredicateForObjectsFromDevices`, in questo caso solo il Apple Watch locale (`HKDevice.LocalDevice`). I due predicati vengono combinati in un predicato composto (`NSCompoundPredicate`) utilizzando il metodo `CreateAndPredicate`.

Viene creato un nuovo `HKAnchoredObjectQuery` per il punto dati desiderato (in questo caso `HKQuantityTypeIdentifier.ActiveEnergyBurned` per il punto dati bruciato energia attivo), non viene imposto alcun limite alla quantità di dati restituiti (`HKSampleQuery.NoLimit`) e viene definito un gestore di aggiornamento per gestire i dati restituiti all'app da HealthKit. 

Il gestore di aggiornamento verrà chiamato ogni volta che vengono recapitati nuovi dati all'app per il punto dati specificato. Se non viene restituito alcun errore, l'app può leggere i dati in modo sicuro, eseguire i calcoli necessari e aggiornare l'interfaccia utente in base alle esigenze.

Il codice esegue il ciclo su tutti gli esempi (`HKSample`) restituiti nella matrice di `addedObjects` e ne esegue il cast in un campione di quantità (`HKQuantitySample`). Ottiene quindi il valore Double dell'esempio come Joule (`HKUnit.Joule`) e lo accumula nel totale parziale dell'energia attiva bruciata per l'allenamento e aggiorna l'interfaccia utente.

### <a name="achieved-goal-notification"></a>Notifica obiettivo riuscita

Come indicato in precedenza, quando l'utente raggiunge un obiettivo nell'app di allenamento, ad esempio il completamento del primo chilometro di esecuzione, può inviare commenti e suggerimenti all'utente tramite il Taptic Engine. L'app deve anche aggiornare l'interfaccia utente in questa fase, poiché l'utente avrà più che probabilmente il polso per visualizzare l'evento che ha richiesto il feedback.

Per riprodurre il feedback tattile, usare il codice seguente:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Osservazione degli eventi

Gli eventi sono timestamp che possono essere usati dall'app per evidenziare determinati punti durante l'allenamento dell'utente. Alcuni eventi verranno creati direttamente dall'app e salvati nell'allenamento e alcuni eventi verranno creati automaticamente da HealthKit.

Per osservare gli eventi creati da HealthKit, l'app eseguirà l'override del metodo `DidGenerateEvent` della `HKWorkoutSessionDelegate`:

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

Apple ha aggiunto i nuovi tipi di evento seguenti in watchos 3:

- `HKWorkoutEventType.Lap` per gli eventi che interrompono l'allenamento in parti di uguale distanza. Ad esempio, per contrassegnare un giro intorno a una traccia durante l'esecuzione.
- `HKWorkoutEventType.Marker`-sono per i punti di interesse arbitrari all'interno dell'allenamento. Ad esempio, il raggiungimento di un punto specifico sul percorso di un'esecuzione esterna.

Questi nuovi tipi possono essere creati dall'app e archiviati nella sessione per un uso successivo nella creazione di grafici e statistiche.

Per creare un evento marcatore, eseguire le operazioni seguenti:

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

Questo codice crea una nuova istanza di un evento marcatore (`HKWorkoutEvent`) e la Salva in una raccolta privata di eventi (che verrà scritta in un secondo momento nella sessione di allenamento) e invia una notifica all'utente dell'evento tramite haptics.

### <a name="pausing-and-resuming-workouts"></a>Sospensione e ripresa degli allenamenti

In qualsiasi momento di una sessione di allenamento, l'utente può sospendere temporaneamente l'allenamento e riprenderlo in un secondo momento. Ad esempio, potrebbero sospendere un'esecuzione interna per effettuare una chiamata importante e riprendere l'esecuzione dopo il completamento della chiamata.

L'interfaccia utente dell'app deve fornire un modo per sospendere e riprendere l'allenamento (chiamando HealthKit) in modo che l'Apple Watch possa conservare sia la potenza che lo spazio dati mentre l'utente ha sospeso l'attività. Inoltre, l'app deve ignorare tutti i nuovi punti dati che potrebbero essere ricevuti quando la sessione di allenamento si trova in uno stato di sospensione.

HealthKit risponderà per sospendere e riprendere le chiamate generando eventi di sospensione e ripresa. Quando la sessione di allenamento viene sospesa, nessun nuovo evento o dato verrà inviato all'app da HealthKit fino alla ripresa della sessione.

Usare il codice seguente per sospendere e riprendere una sessione di allenamento:

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

Gli eventi di sospensione e ripresa che verranno generati da HealthKit possono essere gestiti eseguendo l'override del metodo `DidGenerateEvent` della `HKWorkoutSessionDelegate`:

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

Inoltre, una novità di watchos 3, sono gli eventi Paused (`HKWorkoutEventType.MotionPaused`) e Motion Resumed (`HKWorkoutEventType.MotionResumed`). Questi eventi vengono generati automaticamente da HealthKit durante un allenamento in esecuzione quando l'utente viene avviato e interrotto.

Quando l'app riceve un evento in pausa di movimento, deve arrestare la raccolta dei dati fino a quando l'utente riprende il movimento e viene ricevuto l'evento Motion resumes. L'app non deve sospendere la sessione di allenamento in risposta a un evento in pausa di movimento.

> [!IMPORTANT]
> Gli eventi di movimento in pausa e ripresa del movimento sono supportati solo per il tipo di attività RunningWorkout (`HKWorkoutActivityType.Running`).

Anche in questo caso, questi eventi possono essere gestiti eseguendo l'override del metodo `DidGenerateEvent` del `HKWorkoutSessionDelegate`:

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

## <a name="ending-and-saving-the-workout-session"></a>Terminazione e salvataggio della sessione di allenamento

Quando l'utente ha completato l'allenamento, l'app deve terminare la sessione di allenamento corrente e salvarla nel database HealthKit. Gli allenamenti salvati in HealthKit verranno visualizzati automaticamente nell'elenco attività di allenamento.

Una novità di iOS 10 include anche l'elenco delle attività di allenamento nell'iPhone dell'utente. Quindi, anche se la Apple Watch non è vicina, l'allenamento viene visualizzato sul telefono.

Gli allenamenti che includono gli esempi di energia aggiorneranno l'anello di spostamento dell'utente nell'app attività, in modo che le app di terze parti possano ora contribuire agli obiettivi di spostamento giornalieri dell'utente.

Per terminare e salvare una sessione di allenamento, è necessario eseguire i passaggi seguenti:

[![](workout-apps-images/workout05.png "Ending and Saving the Workout Session Diagram")](workout-apps-images/workout05.png#lightbox)

1. Prima di tutto, l'app deve terminare la sessione di allenamento.
2. La sessione di allenamento viene salvata in HealthKit.
3. Aggiungere eventuali esempi (ad esempio, energia bruciata o distanza) alla sessione di allenamento salvata.

### <a name="ending-the-session"></a>Fine della sessione

Per terminare la sessione di allenamento, chiamare il metodo `EndWorkoutSession` del `HKHealthStore` passando il `HKWorkoutSession`:

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

I sensori dei dispositivi vengono reimpostati in modalità normale. Quando HealthKit termina l'allenamento, riceverà un callback al metodo `DidChangeToState` della `HKWorkoutSessionDelegate`:

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

Una volta terminata la sessione di allenamento, l'app dovrà creare un allenamento (`HKWorkout`) e salvarlo (insieme a un evento) nell'archivio dati HealthKit (`HKHealthStore`):

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

Questo codice crea la quantità totale di energia bruciata e la distanza per l'allenamento come oggetti `HKQuantity`. Viene creato un dizionario di metadati che definiscono l'allenamento e viene specificata la posizione dell'allenamento:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Viene creato un nuovo oggetto `HKWorkout` con lo stesso `HKWorkoutActivityType` del `HKWorkoutSession`, le date di inizio e di fine, l'elenco di eventi (accumulato dalle sezioni precedenti), il dizionario energia, la distanza totale e i metadati. Questo oggetto viene salvato nell'archivio integrità ed eventuali errori gestiti.  

### <a name="adding-samples"></a>Aggiunta di esempi

Quando l'app salva un set di campioni in un allenamento, HealthKit genera una connessione tra gli esempi e l'allenamento, in modo che l'app possa eseguire una query su HealthKit in un secondo momento per tutti gli esempi associati a un determinato allenamento. Usando queste informazioni, l'app può generare grafici dai dati di allenamento e tracciarli in base a una sequenza temporale dell'allenamento.

Per fare in modo che un'app contribuisca all'anello di spostamento dell'app di attività, deve includere campioni di energia con l'allenamento salvato. Inoltre, i totali per distanza ed energia devono corrispondere alla somma di tutti i campioni associati all'app con un allenamento salvato.

Per aggiungere campioni a un allenamento salvato, procedere come segue:

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

Facoltativamente, l'app può calcolare e creare un subset più piccolo di esempi o un mega campione (esteso all'intero intervallo dell'allenamento) che viene quindi associato all'allenamento salvato.

## <a name="workouts-and-ios-10"></a>Allenamenti e iOS 10

Ogni app di allenamento watchos 3 ha un'app di allenamento basata su iOS 10 padre e, una novità di iOS 10, questa app iOS può essere usata per avviare un allenamento che inserisce il Apple Watch in modalità di allenamento (senza intervento dell'utente) ed esegue l'app watchos in modalità di esecuzione in background [(vedere ](#about-background-running)per altri dettagli, eseguire lo sfondo dell'attacco.

Mentre l'app watchos è in esecuzione, può usare WatchConnectivity per la messaggistica e la comunicazione con l'app iOS padre.

Esaminare il funzionamento di questo processo:

[![](workout-apps-images/workout06.png "iPhone and Apple Watch communication diagram")](workout-apps-images/workout06.png#lightbox)

1. L'app iPhone crea un oggetto `HKWorkoutConfiguration` e imposta il tipo di allenamento e il percorso.
2. L'oggetto `HKWorkoutConfiguration` viene inviato alla versione Apple Watch dell'app e, se non è già in esecuzione, viene avviato dal sistema.
3. Usando la configurazione di allenamento passata, l'app watchos 3 avvia una nuova sessione di allenamento (`HKWorkoutSession`).

> [!IMPORTANT]
> Per fare in modo che l'app per iPhone padre avvii un allenamento nella Apple Watch, l'app watchos 3 deve avere abilitato l'esecuzione in background. Per ulteriori informazioni, vedere [Abilitazione dello sfondo eseguito](#enabling-background-running) sopra.

Questo processo è molto simile al processo di avvio diretto di una sessione di allenamento nell'app watchos 3. In iPhone usare il codice seguente:

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

Questo codice garantisce che la versione watchos dell'app sia installata e che la versione di iPhone possa connettersi prima di tutto:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
  ...
}
```

Crea quindi un `HKWorkoutConfiguration` come di consueto e usa il metodo `StartWatchApp` del `HKHealthStore` per inviarlo al Apple Watch e avviare l'app e la sessione di allenamento.

Nell'app Watch OS usare il codice seguente nel `WKExtensionDelegate`:

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

Accetta il `HKWorkoutConfiguration` e crea un nuovo `HKWorkoutSession` e collega un'istanza del `HKWorkoutSessionDelegate`personalizzato. La sessione di allenamento viene avviata con l'archivio integrità HealthKit dell'utente.

## <a name="bringing-all-the-pieces-together"></a>Riunire tutti i componenti

Prendendo in considerazione tutte le informazioni presentate in questo documento, un'app per allenamenti watchos 3 basata sull'app di allenamento basata su iOS 10 potrebbe includere le parti seguenti:

1. **iOS 10 `ViewController.cs`** : gestisce l'avvio di una sessione di connettività Watch e un allenamento sulla Apple Watch.
2. **watchos 3 `ExtensionDelegate.cs`** : gestisce la versione watchos 3 dell'app workout.
3. **watchos 3 `OutdoorRunDelegate.cs`** : un `HKWorkoutSessionDelegate` personalizzato per gestire gli eventi per la sessione.

> [!IMPORTANT]
> Il codice illustrato nelle sezioni seguenti include solo le parti necessarie per implementare le nuove funzionalità migliorate disponibili per le app di allenamento in watchos 3. Tutto il codice di supporto e il codice per presentare e aggiornare l'interfaccia utente non sono inclusi ma possono essere creati facilmente seguendo la documentazione di watchos.<p/>

### <a name="viewcontrollercs"></a>ViewController.cs

Il file di `ViewController.cs` nella versione padre iOS 10 dell'app di allenamento include il codice seguente:

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

Il file di `ExtensionDelegate.cs` nella versione watchos 3 dell'app di allenamento include il codice seguente:

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

Il file di `OutdoorRunDelegate.cs` nella versione watchos 3 dell'app di allenamento include il codice seguente:

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

Apple suggerisce di usare le seguenti procedure consigliate per la progettazione e l'implementazione di app di allenamento in watchos 3 e iOS 10:

- Assicurarsi che l'app di allenamento watchos 3 sia ancora funzionante anche quando non è in grado di connettersi all'iPhone e alla versione iOS 10 dell'app.
- Usare la distanza HealthKit quando il GPS non è disponibile perché è in grado di generare campioni di distanza senza GPS.
- Consente all'utente di avviare l'allenamento dal Apple Watch o dall'iPhone.
- Consente all'app di visualizzare gli allenamenti di altre origini, ad esempio altre app di terze parti, nelle visualizzazioni dei dati cronologici.
- Assicurarsi che l'app non visualizzi gli allenamenti eliminati nei dati cronologici.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti apportati da Apple per l'allenamento delle app in watchos 3 e come implementarli in Xamarin
.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Introduzione a HealthKit](~/ios/platform/healthkit.md)
