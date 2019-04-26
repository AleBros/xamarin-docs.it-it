---
title: CallKit in xamarin. IOS
description: Questo articolo illustra la nuova API CallKit di Apple rilasciata in iOS 10 e come implementarla nelle App xamarin. IOS VOIP.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 6db9ff0085c17f07d07a7591f5d735793bfbc5f9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61390599"
---
# <a name="callkit-in-xamarinios"></a>CallKit in xamarin. IOS

La nuova API CallKit in iOS 10 fornisce un modo per le app VOIP da integrare con l'interfaccia utente di iPhone e fornire un'interfaccia familiare ed esperienze per l'utente finale. Con questa API utenti di visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando l'app telefono **Preferiti** e **recenti** viste.

## <a name="about-callkit"></a>Sulle CallKit

In base a Apple, CallKit è un framework nuovo che eleva 3rd party IP VOIP (Voice Over) le App per un giorno 1 di terze parti esperienza in iOS 10. L'API CallKit consente alle App VOIP di integrare con l'interfaccia utente di iPhone e fornire un'interfaccia familiare ed esperienze per l'utente finale. Proprio come l'app per telefoni incorporata, un utente può visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando l'app telefono **Preferiti** e **recenti** viste.

Inoltre, l'API CallKit offre la possibilità di creare estensioni App che è possibile associare un numero di telefono a un nome (ID chiamante) o indicare il sistema quando deve essere un numero bloccato (chiamata di blocco).

### <a name="the-existing-voip-app-experience"></a>L'esperienza delle app VOIP esistente

Prima di illustrare le funzionalità e la nuova API CallKit, diamo un'occhiata all'esperienza utente corrente con un 3rd party VOIP app in iOS 9 (e minore) usando un'app VOIP fittizia denominata MonkeyCall. MonkeyCall è una semplice app che consente all'utente di inviare e ricevere chiamate VOIP usando le API di iOS esistente.

Attualmente, se l'utente riceve una chiamata in entrata su MonkeyCall e proprio iPhone è bloccato, la notifica ricevuta nella schermata di blocco non è distinguibile da un altro tipo di notifica (come quelli disponibili dai messaggi o le app di posta, ad esempio).

Se l'utente desidera rispondere alla chiamata, dovranno essere diapositiva MonkeyCall notifica per aprire l'app e immettere il passcode (o Touch ID utente) per sbloccare il telefono prima che è stato possibile accettare la chiamata e avvio della conversazione.

L'esperienza è altrettanto complesso se il telefono è sbloccato. Anche in questo caso, la chiamata MonkeyCall in ingresso viene visualizzata come un banner di notifica standard che scorre dalla parte superiore della schermata. Poiché la notifica è temporanea, si è facile dimenticare dall'utente è costretto scegliere di aprire il centro notifiche e trovare la notifica specifica per rispondere, quindi chiamare o trovare e avviare l'app MonkeyCall manualmente.

### <a name="the-callkit-voip-app-experience"></a>L'esperienza delle app CallKit VOIP

Implementando le nuove APIs CallKit nell'app MonkeyCall, l'esperienza dell'utente con una chiamata in ingresso VOIP può essere migliorata notevolmente in iOS 10. Prenda come esempio l'utente che riceve le chiamate VOIP quando il telefono è bloccato in precedenza. Grazie all'implementazione CallKit, la chiamata verrà visualizzato nella schermata di blocco dell'iPhone, esattamente come verrebbe restituito se la chiamata viene ricevuta dall'app per telefoni predefiniti, insieme a schermo intero, interfaccia utente nativa e le funzionalità standard di scorrere rapidamente-a-risposta.

Anche in questo caso, se l'iPhone è sbloccata quando si riceve una chiamata MonkeyCall VOIP, la stessa a schermo intero, interfaccia utente nativa e funzionalità standard di scorrere rapidamente-a-risposta e tap-a-rifiuto del telefono viene presentata l'app e MonkeyCall predefinito ha la possibilità di riproduzione suoneria personalizzata .

CallKit fornisce funzionalità aggiuntive a MonkeyCall, consentendo la VOIP chiama per interagire con altri tipi di chiamate, visualizzazione di elementi recenti incorporato ed elenca preferito, per usare le funzionalità predefinite di blocco e non disturbare, avviare chiamate MonkeyCall da Siri e offre la possibilità per gli utenti assegnare le chiamate MonkeyCall a persone nell'app contatti.

Le sezioni seguenti vengano illustrano l'architettura CallKit, l'ingresso e in uscita chiamare flussi e l'API CallKit in modo dettagliato.

## <a name="the-callkit-architecture"></a>L'architettura CallKit

In iOS 10, Apple ha adottato CallKit in tutti i servizi di sistema in modo che le chiamate effettuate a CarPlay, ad esempio, sono noti all'interfaccia utente di sistema tramite CallKit. Nell'esempio riportato di seguito, poiché MonkeyCall adotta CallKit, è noto al sistema nello stesso modo come questi servizi di sistema predefinite e ottiene tutte le stesse funzionalità:

[![](callkit-images/callkit01.png "The CallKit Service Stack")](callkit-images/callkit01.png#lightbox)

Esaminiamo più da vicino l'App MonkeyCall dal diagramma precedente. L'app contiene tutto il codice per comunicare con la propria rete e le proprie interfacce utente. Collega in CallKit per comunicare con il sistema:

[![](callkit-images/callkit02.png "Architettura dell'App MonkeyCall")](callkit-images/callkit02.png#lightbox)

Sono disponibili due interfacce principali in CallKit usata dall'app:

- `CXProvider` -Consente all'app MonkeyCall informare il sistema di tutte le notifiche out-of-band che potrebbero verificarsi.
- `CXCallController` -Consente all'app MonkeyCall informare il sistema di azioni dell'utente locale.

### <a name="the-cxprovider"></a>Il CXProvider

Come indicato in precedenza, `CXProvider` consente a un'app informare il sistema di tutte le notifiche out-of-band che potrebbero verificarsi. Si tratta di una notifica che non si verificano a causa di azioni dell'utente locale, ma si verificano a causa di eventi esterni, ad esempio le chiamate in ingresso.

Un'app deve usare il `CXProvider` per gli elementi seguenti:

- Una chiamata in ingresso per il sistema di report.
- Segnala che in uscita chiamata è connesso al sistema.
- Report dell'utente remoto che termina la chiamata al sistema.

Quando l'app desidera comunicare al sistema, Usa la `CXCallUpdate` classe e quando il sistema deve comunicare con l'app, viene utilizzato il `CXAction` classe:

[![](callkit-images/callkit03.png "Comunicazione con il sistema tramite un CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>Il CXCallController

Il `CXCallController` consente a un'app informare il sistema di azioni dell'utente locale, ad esempio l'utente che avvia una chiamata VOIP. Implementando un `CXCallController` l'app ottiene a accentuata con altri tipi di chiamate nel sistema. Ad esempio, se è già presente una chiamata telefonica active in corso, `CXCallController` può consentire all'app VOIP effettuare tale chiamata in attesa e avviare o rispondere a una chiamata VOIP.

Un'app deve usare il `CXCallController` per gli elementi seguenti:

- Report quando l'utente è stata avviata una chiamata in uscita al sistema.
- Report quando l'utente risponde una chiamata in ingresso nel sistema.
- Report quando l'utente termina una chiamata al sistema.

Quando l'app desidera comunicare azioni dell'utente locale per il sistema, Usa il `CXTransaction` classe:

[![](callkit-images/callkit04.png "Creazione di report per il sistema utilizzando un CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementazione CallKit

Le sezioni seguenti vengano illustrano come implementare CallKit in un'app xamarin. IOS VOIP. Ai fini di esempio, in questo documento verrà usato codice dall'app MonkeyCall VOIP fittizia. Il codice presentato in questo caso rappresenta diverse classi di supporta, il CallKit parti specifiche saranno descritte dettagliatamente nelle sezioni seguenti.

### <a name="the-activecall-class"></a>La classe ActiveCall

Il `ActiveCall` classe viene usata dall'app MonkeyCall per contenere tutte le informazioni sulle chiamate VOIP che sono attualmente attiva come indicato di seguito:

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall` contiene diverse proprietà che definiscono lo stato della chiamata e due eventi che possono essere generati quando cambia lo stato di chiamata. Poiché questo è solo un esempio, esistono tre metodi utilizzati per simulated avvio, la risposta e interruzione di una chiamata.

### <a name="the-startcallrequest-class"></a>La classe StartCallRequest

Il `StartCallRequest` classe statica, offre alcuni metodi helper che verranno utilizzati durante le chiamate di utilizzo in uscita:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

Il `CallHandleFromURL` e `CallHandleFromActivity` classi vengono utilizzate in AppDelegate per ottenere l'handle contatto della persona chiamata in una chiamata in uscita. Per altre informazioni, vedere la [gestisce le chiamate in uscita](#handling-outgoing-calls) sezione riportata di seguito.

### <a name="the-activecallmanager-class"></a>La classe ActiveCallManager

Il `ActiveCallManager` classe gestisce tutte le chiamate nell'app MonkeyCall.

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

Anche in questo caso, poiché si tratta di una simulazione, solo il `ActiveCallManager` solo gestisce una raccolta di `ActiveCall` degli oggetti e dispone di una routine per la ricerca di una determinata chiamata dal relativo `UUID` proprietà. Include anche metodi per avviare, terminare e modificare lo stato in attesa di una chiamata in uscita. Per altre informazioni, vedere la [gestisce le chiamate in uscita](#handling-outgoing-calls) sezione riportata di seguito.

### <a name="the-providerdelegate-class"></a>La classe ProviderDelegate

Come illustrato in precedenza, un `CXProvider` fornisce la comunicazione bidirezionale tra l'app e il sistema per le notifiche out-of-band. Lo sviluppatore deve fornire una classe personalizzata `CXProviderDelegate` e associarlo al `CXProvider` per l'app per la gestione degli eventi CallKit out-of-band. MonkeyCall utilizza il seguente `CXProviderDelegate`:

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

Quando viene creata un'istanza del delegato, viene passato il `ActiveCallManager` che verrà utilizzato per gestire qualsiasi attività di chiamata. Successivamente, definisce i tipi di handle (`CXHandleType`) che il `CXProvider` rispondere alla richiesta:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

E si ottiene l'immagine modello che verrà applicato per l'icona dell'app quando è in corso una chiamata:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Questi valori ottengano aggregati in un `CXProviderConfiguration` che verrà utilizzato per configurare il `CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

Il delegato crea quindi un nuovo `CXProvider` con queste configurazioni e si collega a esso:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Quando si usa CallKit, l'app non è più verrà creare e gestire i proprio sessioni audio, invece è necessario configurare e usare una sessione di audio che il sistema di creare e gestire per tale. 

Se si trattasse di una vera app, il `DidActivateAudioSession` metodo viene utilizzato per avviare la chiamata con un preconfigurati `AVAudioSession` che il sistema incluse:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Sarebbe inoltre necessario utilizzare il `DidDeactivateAudioSession` fornito il metodo per finalizzare e rilasciare la connessione al sistema audio sessione:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

Il resto del codice verrà descritto dettagliatamente nelle sezioni che seguono.

### <a name="the-appdelegate-class"></a>La classe AppDelegate

MonkeyCall Usa AppDelegate per contenere le istanze di `ActiveCallManager` e `CXProviderDelegate` che verrà usato in tutta l'app:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url); 
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

Il `OpenUrl` e `ContinueUserActivity` sostituzione metodi vengono utilizzati quando l'app sta elaborando una chiamata in uscita. Per altre informazioni, vedere la [gestisce le chiamate in uscita](#handling-outgoing-calls) sezione riportata di seguito.

## <a name="handling-incoming-calls"></a>La gestione delle chiamate in ingresso

Esistono diversi stati e i processi che una chiamata in ingresso VOIP può attraversare durante un tipico flusso di lavoro chiamata in ingresso, ad esempio:

- Per informare l'utente (e il sistema) che una chiamata in ingresso esista.
- Ricevere un avviso se l'utente desidera rispondere alla chiamata e l'inizializzazione della chiamata con l'altro utente.
- Informare il sistema e la rete di comunicazione quando l'utente desidera terminare la chiamata corrente.

Le sezioni seguenti richiederà un quadro dettagliato di come un'app può usare CallKit per Gestione flusso di lavoro chiamata in ingresso, usando l'app MonkeyCall VOIP ad esempio.

### <a name="informing-user-of-incoming-call"></a>Per informare l'utente di chiamata in ingresso

Quando un utente remoto ha avviato una conversazione di VOIP con l'utente locale, si verifica quanto segue:

[![](callkit-images/callkit05.png "Un utente remoto ha avviato una conversazione VOIP")](callkit-images/callkit05.png#lightbox)

1. L'app riceve una notifica dalla propria rete di comunicazioni che vi sia una chiamata in ingresso VOIP.
2. L'app Usa la `CXProvider` per inviare un `CXCallUpdate` al sistema che viene informato della chiamata.
3. Il sistema pubblica la chiamata per l'interfaccia utente di sistema, servizi di sistema e altre App che VOIP usano CallKit.

Ad esempio, nella `CXProviderDelegate`:

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

Questo codice crea un nuovo `CXCallUpdate` dell'istanza e associa un handle che identificherà il chiamante. Successivamente, Usa la `ReportNewIncomingCall` metodo del `CXProvider` classe per informare il sistema della chiamata. Se ha esito positivo, la chiamata viene aggiunto alla raccolta dell'app di chiamate active, in caso contrario, l'errore è necessario segnalare all'utente.

### <a name="user-answering-incoming-call"></a>Chiamata in ingresso risposta utente

Se l'utente desidera rispondere alla chiamata VOIP in ingresso, si verifica quanto segue:

[![](callkit-images/callkit06.png "L'utente risponde alla chiamata in ingresso VOIP")](callkit-images/callkit06.png#lightbox)

1. L'interfaccia utente di sistema informa il sistema che l'utente desidera rispondere alla chiamata VOIP.
2. Il sistema invia un `CXAnswerCallAction` all'app `CXProvider` che viene informato delle finalità di risposta.
3. L'app Usa per comunicare la rete di comunicazione che l'utente è la risposta alla chiamata e la chiamata VOIP continua come di consueto.

Ad esempio, nella `CXProviderDelegate`:

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Questo codice cerca di chiamata specificata nel relativo elenco di chiamate active. Se la chiamata non viene trovata, il sistema riceve una notifica e il metodo viene chiuso. Se viene trovato, il `AnswerCall` metodo di `ActiveCall` classe viene chiamata per avviare la chiamata e il sistema è informazioni se ha esito positivo o negativo.

### <a name="user-ending-incoming-call"></a>Utente finale di chiamata in ingresso

Se l'utente desidera terminare la chiamata da all'interno dell'interfaccia utente dell'app, si verifica quanto segue:

[![](callkit-images/callkit07.png "L'utente termina la chiamata da all'interno dell'interfaccia utente dell'app")](callkit-images/callkit07.png#lightbox)

1. Crea l'app `CXEndCallAction` che ottiene aggregati in un `CXTransaction` che viene inviato al sistema per comunicare che sta per terminare la chiamata.
2. Il sistema verifica l'intento di chiamare End e invia il `CXEndCallAction` tornare all'app tramite il `CXProvider`.
3. L'app segnala quindi la rete di comunicazione che sta per terminare la chiamata.

Ad esempio, nella `CXProviderDelegate`:

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Questo codice cerca di chiamata specificata nel relativo elenco di chiamate active. Se la chiamata non viene trovata, il sistema riceve una notifica e il metodo viene chiuso. Se viene trovato, il `EndCall` metodo di `ActiveCall` classe viene chiamata per terminare la chiamata e il sistema è informazioni se ha esito positivo o negativo. Se l'operazione riesce, la chiamata viene rimosso dalla raccolta di chiamate attive.

## <a name="managing-multiple-calls"></a>La gestione di più chiamate

La maggior parte delle App VOIP può gestire più chiamate in una sola volta. Ad esempio, se è presente attualmente una chiamate VOIP attivo e consente di sospendere l'app Ottiene la notifica che esiste un una nuova chiamata in ingresso, l'utente o disconnessione alla prima chiamata a rispondere al secondo.

Nell'assegnare situazione precedente, il sistema invierà un `CXTransaction` all'app, che includerà un elenco di più azioni (, ad esempio il `CXEndCallAction` e il `CXAnswerCallAction`). Tutte queste azioni verranno devono essere soddisfatti singolarmente, in modo che il sistema può aggiornare l'interfaccia utente in modo appropriato.

## <a name="handling-outgoing-calls"></a>La gestione in uscita chiama

Se l'utente tocca una voce nell'elenco di elementi recenti (nell'app per telefoni), ad esempio, che proviene da una chiamata che appartengono all'app, verrà inviato un _finalità di chiamare Start_ dal sistema:

[![](callkit-images/callkit08.png "Ricezione di un programma di chiamata di avvio")](callkit-images/callkit08.png#lightbox)

1. L'app creerà un _avvia azione chiama_ base all'avvio chiamare intento che ha ricevuto dal sistema. 
2. L'app userà il `CXCallController` per richiedere l'azione di avvio chiamata dal sistema.
3. Se il sistema accetta l'azione, verrà restituito all'app tramite il `XCProvider` delegare.
4. L'app viene avviata la chiamata in uscita con la rete di comunicazione.

Per ulteriori informazioni sulla finalità, vedere la [Intent e le estensioni dell'interfaccia utente Intent](~/ios/platform/sirikit/understanding-sirikit.md) documentazione. 

### <a name="the-outgoing-call-lifecycle"></a>Il ciclo di vita di chiamata in uscita

Quando si lavora con CallKit e una chiamata in uscita, l'app dovrà informare il sistema degli eventi del ciclo di vita seguenti:

1. **A partire** -informare il sistema sta per iniziare una chiamata in uscita.
2. **Avviato** -informare il sistema che è stata avviata una chiamata in uscita.
3. **La connessione** -informare il sistema che si connette la chiamata in uscita.
4. **Connesso** -comunicare in uscita chiamata si è connesso e che entrambe le parti possono ora comunicare con.

Il codice seguente, ad esempio, verrà avviata una chiamata in uscita:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Crea un `CXHandle` e lo usa per configurare un `CXStartCallAction` che viene incluso in un `CXTransaction` vale a dire inviato al sistema usando la `RequestTransaction` metodo il `CXCallController` classe. Chiamando il `RequestTransaction` metodo, il sistema è possibile inserire qualsiasi esistente chiamate in sospeso, indipendentemente dall'origine (app per telefoni, FaceTime, VOIP, e così via), prima che inizi la nuova chiamata.

La richiesta di avvio di una chiamata in uscita VOIP può provenire da numerose origini diverse, come Siri, una voce in una scheda di contatto (nell'app contatti) o nell'elenco di elementi recenti (nell'app per telefoni). In queste situazioni, l'app verrà inviato un intento chiamata di avvio all'interno di un `NSUserActivity` e sarà necessario gestirlo AppDelegate:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

In questo caso il `CallHandleFromActivity` metodo della classe helper `StartCallRequest` viene utilizzata per ottenere l'handle alla persona chiamata (vedere [classe StartCallRequest](#the-startcallrequest-class) sopra).

Il `PerformStartCallAction` metodo per il [ProviderDelegate classe](#the-providerdelegate-class) viene usato per avviare la chiamata in uscita effettiva infine e informare il sistema del ciclo di vita:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Crea un'istanza del `ActiveCall` classe (per contenere le informazioni relative alla chiamata in corso) e popola con la persona chiamata. Il `StartingConnectionChanged` e `ConnectedChanged` gli eventi vengono usati per monitorare e segnalare il ciclo di vita di chiamata in uscita. La chiamata viene avviata e il sistema informati che è stata eseguita l'azione.

### <a name="ending-an-outgoing-call"></a>Termina una chiamata in uscita

Quando l'utente ha terminato una chiamata in uscita e richieste per terminarla, può essere utilizzato il codice seguente:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Se consente di creare un `CXEndCallAction` con l'UUID della chiamata alla fine, lo Raggruppa in un `CXTransaction` vale a dire inviato al sistema usando la `RequestTransaction` metodo del `CXCallController` classe. 

## <a name="additional-callkit-details"></a>Dettagli aggiuntivi CallKit

Questa sezione illustra alcuni dettagli aggiuntivi che lo sviluppatore avrà bisogno di prendere in considerazione quando si lavora con CallKit, ad esempio:

- Configurazione del provider
- Errori relativi alle azioni
- Restrizioni di sistema
- VOIP Audio

### <a name="provider-configuration"></a>Configurazione del provider

La configurazione del provider consente a un'app per iOS 10 VOIP personalizzare l'esperienza dell'utente (all'interno di interfaccia utente nativa nella chiamata) quando utilizzano CallKit.

Un'app può effettuare le seguenti personalizzazioni:

- Visualizzare il nome localizzato.
- Abilitare il supporto di chiamata video.
- Personalizzare i pulsanti nell'interfaccia utente In chiamata presentando la propria icona immagine modello. L'interazione dell'utente con pulsanti personalizzati viene inviato direttamente all'app da elaborare. 

### <a name="action-errors"></a>Errori relativi alle azioni

App per iOS 10 VOIP usando CallKit necessario gestire le azioni non superati normalmente e mantenere l'utente è informato lo stato dell'azione in qualsiasi momento. 

Considerare l'esempio seguente:

1. L'app ha ricevuto un'azione chiama Start e ha iniziato il processo di inizializzazione di una nuova chiamata VOIP con la rete di comunicazione.
2. A causa di limitata o nessuna funzionalità di comunicazione di rete, questa connessione ha esito negativo.
3. L'app *devono* invia le **esito negativo** messaggio all'azione chiama avvio (`Action.Fail()`) per informare il sistema dell'errore.
4. In questo modo il sistema informare l'utente dello stato della chiamata. Ad esempio, per visualizzare l'interfaccia utente di chiamare un errore.

Inoltre, necessario rispondere a un'app per iOS 10 VOIP _errori di Timeout_ che può verificarsi quando un'azione previsto non può essere elaborata entro un determinato periodo di tempo. Ogni tipo di azione forniti da CallKit ha un valore di Timeout massimo è associato. Questi valori di Timeout assicurarsi che qualsiasi azione CallKit richiesto dall'utente viene gestita in modo reattivo, in questo modo mantenendo il sistema operativo fluide e reattive anche.

Esistono diversi metodi sul delegato Provider (`CXProviderDelegate`) che deve essere sottoposto a override per gestire correttamente anche questa situazioni di Timeout.

### <a name="system-restrictions"></a>Restrizioni di sistema

Basato sullo stato corrente del dispositivo iOS in esecuzione l'app per iOS 10 VOIP, possibile che venga imposto alcune restrizioni di sistema.

Ad esempio, una chiamata in ingresso VOIP può essere limitata dal sistema se:

1. La persona che chiama è nell'elenco di chiamante bloccati dell'utente.
2. Dispositivo iOS dell'utente è in modalità-non disturbare.

Se le chiamate VOIP sono limitata da una di queste situazioni, è possibile usare il codice seguente per gestirlo:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);
    
        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>Audio VOIP

CallKit offre diversi vantaggi per gestire le risorse audio che richiederà un'app per iOS 10 VOIP durante le chiamate VOIP in tempo reale. Uno dei vantaggi principali è l'app audio sessione verrà elevati priorità durante l'esecuzione in iOS 10. Si tratta del livello di priorità stesso come incorporato nel telefono e le app FaceTime e questo livello di priorità avanzata impedirà altre App in esecuzione di interrompere la VOIP sessione dell'app audio.

Inoltre, CallKit può accedere a altri hint di routing audio che può migliorare le prestazioni e indirizzare in modo intelligente audio VOIP ai dispositivi di output specifico durante una chiamata in tempo reale in base alle preferenze utente e stati dei dispositivi. Ad esempio, in base i dispositivi collegati, ad esempio le cuffie bluetooth, una connessione dinamica CarPlay o le impostazioni di accessibilità.

Durante il ciclo di vita di un tipico VOIP chiamare usando CallKit, sarà necessario configurare il Stream Audio che CallKit fornirà l'app. Dare un'occhiata al seguente:

[![](callkit-images/callkit09.png "La sequenza di azioni di chiamata di avvio")](callkit-images/callkit09.png#lightbox)

1. Un'azione chiama Start viene ricevuta dall'app per rispondere a una chiamata in ingresso.
2. Fornisce la configurazione sarà necessario per prima questa azione viene acquisita dall'app, relativo `AVAudioSession`.
3. L'app informa il sistema che l'azione è stato acquisito.
4. Prima che si connette la chiamata, CallKit fornisce una priorità alta `AVAudioSession` corrispondente alla configurazione richiesto dall'app. L'app riceverà una notifica tramite il `DidActivateAudioSession` metodo del relativo `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Utilizzo di estensioni della directory chiamata

Quando si lavora con, CallKit _chiamare le estensioni della Directory_ forniscono un modo per aggiungere i numeri di chiamate bloccati e identificare i numeri che sono specifici per una determinata app VOIP ai contatti nell'app contatti nel dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementazione di un'estensione di directory chiamata

Per implementare un'estensione per elenco telefonico in un'app xamarin. IOS, effettuare le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **Esplora soluzioni** e selezionare **Add** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **chiamare le estensioni della Directory** e fare clic su di **successivo** pulsante: 

    [![](callkit-images/calldir01.png "Creazione di una nuova estensione per elenco telefonico")](callkit-images/calldir01.png#lightbox)
4. Immettere un **Name** per l'estensione e fare clic sui **successivo** pulsante: 

    [![](callkit-images/calldir02.png "Immettere un nome per l'estensione")](callkit-images/calldir02.png#lightbox)
5. Modificare il **nome progetto** e/o **Nome soluzione** se necessario e fare clic sui **crea** pulsante: 

    [![](callkit-images/calldir03.png "Creazione del progetto")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione dell'app in Visual Studio.
2. Fare clic sul nome della soluzione nel **Esplora soluzioni** e selezionare **Add** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **chiamare le estensioni della Directory** e fare clic su di **successivo** pulsante: 

    [![](callkit-images/calldir01w.png "Creazione di una nuova estensione per elenco telefonico")](callkit-images/calldir01.png#lightbox)
4. Immettere un **Name** per l'estensione e fare clic sui **OK** pulsante

-----

Verrà aggiunto un `CallDirectoryHandler.cs` classe al progetto che è simile a quanto segue:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

Il `BeginRequest` metodo nel gestore della Directory chiamare dovrà essere modificato per fornire le funzionalità richieste. Nel caso dell'esempio precedente, tenta di impostare l'elenco dei numeri bloccati e disponibili nel database dei contatti dell'app VOIP. Se una richiesta ha esito negativo per qualsiasi motivo, creare un `NSError` descrivere l'errore e viene passato il `CancelRequest` metodo del `CXCallDirectoryExtensionContext` classe.

Per impostare l'utilizzo di numeri di bloccate il `AddBlockingEntry` metodo del `CXCallDirectoryExtensionContext` classe. I numeri forniti al metodo _necessario_ essere in ordine numerico crescente. Per ottenere prestazioni ottimali e l'utilizzo di memoria quando sono presenti che molti numeri di telefono, prendere in considerazione solo il caricamento di un subset dei numeri in un determinato momento e usando i pool autorelease per rilasciare gli oggetti allocati durante ogni batch di numeri che vengono caricati.

Per comunicare all'app di contattare dei numeri di telefono noti all'app VOIP, usare il `AddIdentificationEntry` metodo di `CXCallDirectoryExtensionContext` classe e specificare il numero e un'etichetta di identificazione. Anche in questo caso, i numeri forniti al metodo _necessario_ essere in ordine numerico crescente. Per ottenere prestazioni ottimali e l'utilizzo di memoria quando sono presenti che molti numeri di telefono, prendere in considerazione solo il caricamento di un subset dei numeri in un determinato momento e usando i pool autorelease per rilasciare gli oggetti allocati durante ogni batch di numeri che vengono caricati.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la nuova API CallKit di Apple rilasciata in iOS 10 e come implementarla nelle App xamarin. IOS VOIP. Abbia illustrato in che modo CallKit consente a un'app integrare il sistema iOS, come offre parità delle funzionalità con le app predefinite (ad esempio Phone) e come aumenta la visibilità di un'app in iOS in posizioni, ad esempio il blocco e le schermate Home, tramite le interazioni Siri e tutto le app dei contatti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
