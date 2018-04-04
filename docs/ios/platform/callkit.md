---
title: CallKit
description: Questo articolo descrive la nuova API CallKit che Apple rilasciate in iOS 10 e come implementarlo in App xamarin VOIP.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 67c761aa6656b571f16632dd1a076ff11737a424
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="callkit"></a>CallKit

_Questo articolo descrive la nuova API CallKit che Apple rilasciate in iOS 10 e come implementarlo in App xamarin VOIP._


La nuova API CallKit in iOS 10 fornisce un modo per le app VOIP da integrare con l'iPhone dell'interfaccia utente e fornire un'interfaccia familiare ed esperienza per l'utente finale. Con questa API può visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti con l'app di Phone **Preferiti** e **recenti** viste.

## <a name="about-callkit"></a>Su CallKit

In base a Apple, CallKit è un nuovo framework che eleva App IP VOIP (Voice Over) di parti 3rd parti di un 1 ° esperienza in iOS 10. L'API CallKit consente alle App VOIP di integrare l'iPhone dell'interfaccia utente e fornire un'interfaccia familiare ed esperienza per l'utente finale. Come app telefonica predefinita, un utente può visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti con l'app di Phone **Preferiti** e **recenti** viste.

Inoltre, l'API di CallKit fornisce la possibilità di creare estensioni per App che è possibile associare un numero di telefono con un nome (ID chiamante) o indicare il sistema quando deve essere un numero bloccato (chiamata di blocco).

### <a name="the-existing-voip-app-experience"></a>L'esperienza di App VOIP esistente

Prima di esaminare la capacità e la nuova API CallKit, osservare l'esperienza dell'utente corrente con un 3rd party VOIP app in iOS 9 (e minore) usando un'app VOIP fittizia denominata MonkeyCall. MonkeyCall è una semplice app che consente all'utente di inviare e ricevere le chiamate VOIP utilizzando le API di iOS esistente.

Attualmente, se l'utente riceve una chiamata in ingresso su MonkeyCall e i relativi iPhone è bloccato, la notifica ricevuta nella schermata di blocco non è distinguibile da qualsiasi altro tipo di notifica (simile a quelle dei messaggi o le app di posta, ad esempio).

Se si desidera rispondere alla chiamata, dovranno essere diapositiva la notifica MonkeyCall per aprire l'app e immettere il passcode (o Touch ID utente) per sbloccare il telefono prima che può accettare la chiamata e avvio della conversazione.

L'esperienza è ugualmente complesso se il telefono è sbloccato. Nuovamente, la chiamata di MonkeyCall in ingresso viene visualizzata come un banner di notifica standard diapositive dalla parte superiore della schermata. Poiché la notifica è temporanea, può essere perso facilmente dall'utente portando per aprire il centro notifiche e trovare la notifica per rispondere, quindi chiamare o trovare e avviare manualmente l'app MonkeyCall specifica.

### <a name="the-callkit-voip-app-experience"></a>L'esperienza di App VOIP CallKit

Implementando le nuove APIs CallKit nell'app MonkeyCall, l'esperienza dell'utente con una chiamata VOIP in ingresso può essere notevolmente migliorata in iOS 10. Prendere in considerazione l'utente che riceve una chiamata VOIP quando il telefono è stato bloccato dall'alto. Implementando CallKit, la chiamata verrà visualizzato nella schermata di blocco dell'iPhone, esattamente come se la chiamata viene ricevuta da app telefonica predefinita, con schermo intero, interfaccia utente nativa e le funzionalità standard per scorrere-risposta.

Nuovamente, se iPhone viene sbloccata quando viene ricevuta una chiamata VOIP MonkeyCall, la stessa a schermo intero, interfaccia utente nativa e standard scorrere-a-risposta tap-a-rifiuto funzionalità e del telefono viene presentata l'app e MonkeyCall incorporato ha la possibilità di riprodurre una suoneria personalizzata .

CallKit fornisce funzionalità aggiuntive a MonkeyCall, consentendo il VOIP chiama per interagire con altri tipi di chiamate, verrà visualizzato nella recenti incorporato e preferito Elenca, per usare le funzionalità incorporate di non eseguire disturbi e di blocco, avviare MonkeyCall chiamate da Siri e offre la possibilità per gli utenti assegnare MonkeyCall chiamate a persone nell'app contatti.

Le seguenti sezioni illustra l'architettura CallKit, l'ingresso e in uscita chiamare flussi e l'API CallKit in modo dettagliato.


## <a name="the-callkit-architecture"></a>L'architettura CallKit

In iOS 10, Apple ha adottato CallKit in tutti i servizi di sistema in modo che le chiamate effettuate a CarPlay, ad esempio, sono noti per l'interfaccia utente di sistema tramite CallKit. Nell'esempio indicato di seguito, poiché MonkeyCall adotta CallKit, è noto al sistema nello stesso modo come questi servizi di sistema predefinite e tutti i le stesse funzionalità:

[![](callkit-images/callkit01.png "Lo Stack del servizio CallKit")](callkit-images/callkit01.png#lightbox)

Esaminare più vicino all'App MonkeyCall dal diagramma precedente. L'app contiene tutto il codice per comunicare con una propria rete e le proprie interfacce utente. È collegato in CallKit per comunicare con il sistema:

[![](callkit-images/callkit02.png "Architettura App MonkeyCall")](callkit-images/callkit02.png#lightbox)

Sono disponibili due interfacce principali in CallKit che usa l'app:

- `CXProvider` -Consente all'app MonkeyCall informare il sistema di tutte le notifiche fuori banda che potrebbero verificarsi.
- `CXCallController` -Consente all'app MonkeyCall informare il sistema di azioni utente locale.

### <a name="the-cxprovider"></a>Il CXProvider

Come descritto in precedenza, `CXProvider` consente a un'app informare il sistema di tutte le notifiche fuori banda che potrebbero verificarsi. Si tratta di notifica che non si verificano a causa di azioni dell'utente locale, ma si verificano a causa di eventi esterni, ad esempio chiamate in ingresso.

Un'applicazione deve utilizzare il `CXProvider` per gli elementi seguenti:

- Chiamata in entrata al server di report.
- Report che in uscita di chiamata è connesso al sistema.
- L'utente remoto termina la chiamata al sistema di report.

Quando l'app desidera comunicare al sistema, utilizza il `CXCallUpdate` classe e quando il sistema deve comunicare con l'app, Usa il `CXAction` classe:

[![](callkit-images/callkit03.png "La comunicazione con il sistema tramite un CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>Il CXCallController

Il `CXCallController` consente a un'app informare il sistema di azioni dell'utente locale, ad esempio l'utente che avvia una chiamata VOIP. Implementando un `CXCallController` l'app Ottiene all'interazione con altri tipi di chiamate nel sistema. Ad esempio, se è già presente una chiamata di telefonia active in corso, `CXCallController` può consentire all'app VOIP per effettuare tale chiamata in attesa e avviare o rispondere a una chiamata VOIP.

Un'applicazione deve utilizzare il `CXCallController` per gli elementi seguenti:

- Report quando l'utente è stata avviata una chiamata in uscita al sistema.
- Report quando l'utente risponde a una chiamata al sistema in ingresso.
- Report quando l'utente termina una chiamata al sistema.

Quando l'app desidera comunicare azioni dell'utente locale per il sistema, viene utilizzata la `CXTransaction` classe:

[![](callkit-images/callkit04.png "Creazione di report per il sistema utilizzando un CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementazione CallKit

Nelle sezioni seguenti verranno illustrato come implementare CallKit in un'app xamarin VOIP. Ai fini di esempio, questo documento verrà usato codice dall'app MonkeyCall VOIP fittizio. Il codice presentato in questo campo rappresenta diverse classi di supporto, la CallKit parti specifiche verranno descritti in dettaglio nelle sezioni seguenti.

### <a name="the-activecall-class"></a>La classe ActiveCall

La `ActiveCall` classe viene utilizzata dall'app MonkeyCall per contenere tutte le informazioni sulle chiamate VOIP che sono attualmente attiva, come indicato di seguito:

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

`ActiveCall` contiene diverse proprietà che definiscono lo stato della chiamata e due eventi che possono essere generati quando cambia lo stato della chiamata. Poiché questo è solo un esempio, esistono tre metodi utilizzati per simulare l'avvio, la risposta e fine di una chiamata.

### <a name="the-startcallrequest-class"></a>La classe StartCallRequest

La `StartCallRequest` classe statica, fornisce alcuni metodi di supporto che verranno utilizzati quando viene chiamato utilizzo in uscita:

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

Il `CallHandleFromURL` e `CallHandleFromActivity` classi vengono utilizzate nel AppDelegate per ottenere l'handle di contatto della persona che la chiamata in una chiamata in uscita. Per ulteriori informazioni, vedere il [gestisce le chiamate in uscita](#Handling-Outgoing-Calls) sezione riportata di seguito.

### <a name="the-activecallmanager-class"></a>La classe ActiveCallManager

La `ActiveCallManager` classe gestisce le chiamate aperte nell'app MonkeyCall.

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
                if (call.UUID == uuid) return call;
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

Nuovamente, poiché si tratta di una simulazione, solo il `ActiveCallManager` solo gestisce una raccolta di `ActiveCall` oggetti e dispone di una routine per la ricerca di una chiamata specificata dal relativo `UUID` proprietà. Include anche metodi per avviare, terminare e modificare lo stato in attesa di una chiamata in uscita. Per ulteriori informazioni, vedere il [gestisce le chiamate in uscita](#Handling-Outgoing-Calls) sezione riportata di seguito.

### <a name="the-providerdelegate-class"></a>La classe ProviderDelegate

Come illustrato in precedenza, un `CXProvider` fornisce la comunicazione bidirezionale tra l'app e il sistema per le notifiche fuori banda. Lo sviluppatore deve fornire un oggetto personalizzato `CXProviderDelegate` e associarlo al `CXProvider` per l'app gestire gli eventi CallKit fuori banda. MonkeyCall utilizza il seguente `CXProviderDelegate`:

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

            // Get Image Mask
            var maskImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconMaskImageData = maskImage.AsPNG(),
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
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNsDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNsDate ());
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

Quando viene creata un'istanza del delegato, viene passato il `ActiveCallManager` che verrà utilizzato per gestire qualsiasi attività di chiamata. Successivamente, definisce i tipi di handle (`CXHandleType`) che il `CXProvider` risponderà:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

E si ottiene la maschera che verrà applicata all'icona dell'app quando è in corso una chiamata:

```csharp
// Get Image Mask
var maskImage = UIImage.FromFile ("telephone_receiver.png");
```

Questi valori ottengano inseriti in un `CXProviderConfiguration` che verrà utilizzato per configurare il `CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconMaskImageData = maskImage.AsPNG(),
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

Quando si utilizza CallKit, l'app verrà non più di creare e gestire i proprio sessioni audio, sarà invece necessario configurare e utilizzare una sessione audio che il sistema di creare e gestire per tale. 

Se si trattasse di una vera app, il `DidActivateAudioSession` metodo viene utilizzato per avviare la chiamata con un preconfigurato `AVAudioSession` forniti dal sistema:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Viene inoltre utilizzato il `DidDeactivateAudioSession` metodo per rendere effettive e rilasciare la connessione al sistema fornito sessione audio:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

Il resto del codice verrà descritta in dettaglio nelle sezioni che seguono.

### <a name="the-appdelegate-class"></a>La classe AppDelegate

MonkeyCall utilizza il AppDelegate per contenere le istanze di `ActiveCallManager` e `CXProviderDelegate` che verrà utilizzato nell'intera app:

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

Il `OpenUrl` e `ContinueUserActivity` override vengono usati metodi durante l'applicazione sta elaborando una chiamata in uscita. Per ulteriori informazioni, vedere il [gestisce le chiamate in uscita](#Handling-Outgoing-Calls) sezione riportata di seguito.

## <a name="handling-incoming-calls"></a>La gestione delle chiamate in ingresso

Esistono diversi stati e i processi da una chiamata VOIP in ingresso può essere eseguita per durante un tipico flusso di lavoro chiamate in ingresso, ad esempio:

- Informare l'utente (e il sistema), che esista una chiamata in ingresso.
- Ricezione di notifiche quando l'utente desidera rispondere alla chiamata e inizializzando la chiamata con l'altro utente.
- Informa il sistema e la comunicazione di rete quando l'utente desidera terminare la chiamata corrente.

Nelle sezioni seguenti avranno un'analisi approfondita di un'applicazione può utilizzare CallKit per gestire in ingresso chiamata flusso di lavoro, ad esempio usando l'app MonkeyCall VOIP.

### <a name="informing-user-of-incoming-call"></a>Informare l'utente di chiamate in ingresso

Quando un utente remoto ha avviato una conversazione VOIP con l'utente locale, si verifica quanto segue:

[![](callkit-images/callkit05.png "Un utente remoto ha avviato una conversazione VOIP")](callkit-images/callkit05.png#lightbox)

1. L'app Ottiene una notifica dalla sua rete di comunicazioni che vi sia una chiamata VOIP in ingresso.
2. L'applicazione utilizzi la `CXProvider` per inviare un `CXCallUpdate` al sistema che viene informato della chiamata.
3. Il sistema pubblica la chiamata per l'interfaccia utente di sistema, servizi di sistema e qualsiasi altra App VOIP utilizzando CallKit.

Ad esempio, nel `CXProviderDelegate`:

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

Questo codice crea un nuovo `CXCallUpdate` istanza e associa un handle che identificherà il chiamante. Viene quindi utilizzato il `ReportNewIncomingCall` metodo la `CXProvider` classe per informare il sistema della chiamata. Se ha esito positivo, la chiamata viene aggiunto alla raccolta dell'app di chiamate attive, in caso contrario, l'errore dovrà essere comunicati all'utente.

### <a name="user-answering-incoming-call"></a>Chiamate in ingresso risposta utente

Se l'utente desidera rispondere alla chiamata VOIP in ingresso, si verifica quanto segue:

[![](callkit-images/callkit06.png "L'utente risponde alla chiamata VOIP in ingresso")](callkit-images/callkit06.png#lightbox)

1. L'interfaccia utente di sistema informa il sistema che l'utente desidera rispondere alla chiamata VOIP.
2. Il sistema invia un `CXAnswerCallAction` all'app `CXProvider` che viene informato delle finalità di risposta.
3. L'app indica la rete di comunicazione che l'utente risponde alla chiamata e la chiamata VOIP procede nel modo consueto.

Ad esempio, nel `CXProviderDelegate`:

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

Questo codice cerca chiamata specificata nell'elenco delle chiamate attive. Se la chiamata non viene trovata, il sistema riceve una notifica e il metodo termina. Se viene trovato, il `AnswerCall` metodo la `ActiveCall` classe viene chiamata per avviare la chiamata e il sistema di informazioni se ha esito positivo o negativo.

### <a name="user-ending-incoming-call"></a>Utente finale di chiamate in ingresso

Se l'utente desidera terminare la chiamata da all'interno dell'interfaccia utente dell'applicazione, si verifica quanto segue:

[![](callkit-images/callkit07.png "L'utente termina la chiamata da all'interno dell'interfaccia utente dell'applicazione")](callkit-images/callkit07.png#lightbox)

1. L'app crea `CXEndCallAction` che ottiene inserita in un `CXTransaction` che viene inviato al sistema per comunicare che sta terminando la chiamata.
2. Il sistema verifica lo scopo di chiamare End e invia il `CXEndCallAction` tornare all'app tramite il `CXProvider`.
3. L'app comunica quindi la rete di comunicazione che sta terminando la chiamata.

Ad esempio, nel `CXProviderDelegate`:

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

Questo codice cerca chiamata specificata nell'elenco delle chiamate attive. Se la chiamata non viene trovata, il sistema riceve una notifica e il metodo termina. Se viene trovato, il `EndCall` metodo la `ActiveCall` classe viene chiamata per terminare la chiamata e il sistema di informazioni se ha esito positivo o negativo. Se ha esito positivo, la chiamata viene rimosso dalla raccolta di chiamate attive.

## <a name="managing-multiple-calls"></a>La gestione di più chiamate

La maggior parte delle App VOIP può gestire più chiamate in una sola volta. Ad esempio, se è attualmente una chiamata VOIP attiva e l'app Ottiene la notifica che esiste un una nuova chiamata in ingresso, l'utente è possibile sospendere o disconnessione alla prima chiamata a rispondere al secondo.

In offrono la situazione precedente, il sistema invierà un `CXTransaction` all'app che includerà un elenco di più operazioni (ad esempio il `CXEndCallAction` e `CXAnswerCallAction`). Tutte queste azioni verranno devono essere soddisfatti singolarmente, in modo che il sistema è possibile aggiornare l'interfaccia utente in modo appropriato.

## <a name="handling-outgoing-calls"></a>Gestisce le chiamate in uscita

Se l'utente digita una voce dall'elenco recenti (nell'app telefono), ad esempio, che è una chiamata che appartengono all'app, verrà inviato un _con finalità di chiamare Start_ dal sistema:

[![](callkit-images/callkit08.png "Ricezione di un intento di chiamata di Start")](callkit-images/callkit08.png#lightbox)

1. L'app creerà un _azione di avvio chiamare_ in base all'avvio chiamare intento ricevuto dal sistema. 
2. L'app userà il `CXCallController` per richiedere l'azione di avvio chiamata dal sistema.
3. Se il sistema accetta l'azione, verrà restituito all'app tramite il `XCProvider` delegato.
4. L'app viene avviata la chiamata in uscita con la rete di comunicazione.

Per ulteriori informazioni sui tipi, vedere il nostro [tipi e le estensioni dell'interfaccia utente intenti](~/ios/platform/sirikit/understanding-sirikit.md) documentazione. 

### <a name="the-outgoing-call-lifecycle"></a>Il ciclo di vita di chiamata in uscita

Quando si lavora con CallKit e una chiamata in uscita, è necessario informare il sistema dei seguenti eventi del ciclo di vita dell'app:

1. **Avvio** -informare il sistema sta per avviare una chiamata in uscita.
2. **Avvio** -informare il sistema che è stata avviata una chiamata in uscita.
3. **Connessione** -informare il sistema che si connette la chiamata in uscita.
4. **Connesso** -informare in uscita chiamata è connesso e che entrambe le parti possono ora comunicare con.

Ad esempio, il codice seguente è possibile avviare una chiamata in uscita:

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

Crea un `CXHandle` e viene utilizzato per configurare un `CXStartCallAction` che viene inserita in un `CXTransaction` ovvero inviato al sistema tramite il `RequestTransaction` metodo il `CXCallController` classe. Chiamando il `RequestTransaction` (metodo), il sistema è possibile inserire qualsiasi esistente chiamate in sospeso, indipendentemente dall'origine (app Phone, FaceTime, VOIP, e così via), prima che inizi la nuova chiamata.

La richiesta per avviare una chiamata VOIP in uscita può provenire da diverse origini diverse, ad esempio Siri, una voce in una scheda contatto (nell'app contatti) o dall'elenco recenti (nell'app telefono). In questi casi, l'app verrà inviato un intento chiamata di Start all'interno di un `NSUserActivity` e sarà necessario il AppDelegate da gestire:

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

In questo caso il `CallHandleFromActivity` metodo della classe helper `StartCallRequest` viene utilizzato per ottenere l'handle per l'utente viene chiamato (vedere [StartCallRequest classe](#The-StartCallRequest-Class) sopra). 

Il `PerformStartCallAction` metodo il [ProviderDelegate classe](#The-ProviderDelegate-Class) viene utilizzato per avviare la chiamata in uscita effettiva infine e informare il sistema del ciclo di vita:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNsDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNsDate ());
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

Crea un'istanza di `ActiveCall` classe (per contenere le informazioni sulla chiamata in corso) e popola con l'utente che la chiamata. Il `StartingConnectionChanged` e `ConnectedChanged` gli eventi vengono utilizzati per monitorare e segnalare il ciclo di vita di chiamata in uscita. La chiamata viene avviata e viene informato che l'azione è stata eseguita.

### <a name="ending-an-outgoing-call"></a>Termina una chiamata in uscita

Quando l'utente ha terminato con una chiamata in uscita e voglia terminarla, è possibile utilizzare il codice seguente:

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

Se crea un `CXEndCallAction` con UUID della chiamata alla fine, Raggruppa in un `CXTransaction` ovvero inviato al sistema tramite il `RequestTransaction` metodo la `CXCallController` classe. 

## <a name="additional-callkit-details"></a>Dettagli aggiuntivi CallKit

In questa sezione illustra alcuni dettagli aggiuntivi che lo sviluppatore dovrà prendere in considerazione quando si lavora con CallKit, ad esempio:

- Configurazione del provider
- Errori relativi alle azioni
- Restrizioni di sistema
- VOIP Audio

### <a name="provider-configuration"></a>Configurazione del provider

La configurazione del provider consente a un'app VOIP iOS 10 personalizzare l'esperienza dell'utente (all'interno di interfaccia utente nativa nella chiamata) quando l'utilizzo di CallKit.

Un'app può effettuare le seguenti personalizzazioni:

- Visualizzare un nome localizzato.
- Abilitare il supporto di chiamate video.
- Personalizzare i pulsanti nell'interfaccia utente In chiamata presentando un'icona di immagine mascherata. Interazione dell'utente con pulsanti personalizzati viene inviato direttamente all'app da elaborare. 

### <a name="action-errors"></a>Errori relativi alle azioni

App per iOS 10 VOIP utilizzando CallKit necessario gestire le azioni non superati normalmente e mantenere l'utente informato dello stato di azione in qualsiasi momento. 

Considerare l'esempio seguente:

1. L'app ha ricevuto un'azione di chiamare Start e ha iniziato il processo di inizializzazione di una nuova chiamata VOIP con la rete di comunicazione.
2. A causa di limitato o alcuna funzionalità di comunicazione di rete, questa connessione ha esito negativo.
3. L'app *deve* invia il **esito negativo** messaggio all'azione di chiamare Start (`Action.Fail()`) per informare il sistema dell'errore.
4. Questo consente al sistema informare l'utente dello stato della chiamata. Ad esempio, per visualizzare l'interfaccia utente di chiamare un errore.

È inoltre necessario rispondere a un'app per iOS 10 VOIP _errori di Timeout_ che può verificarsi quando un'azione previsto non può essere elaborata all'interno di un determinato periodo di tempo. Ogni tipo di azione fornito da CallKit presenta un valore di Timeout massimo è associato. Questi valori di Timeout assicurarsi che qualsiasi azione CallKit richiesta dall'utente è gestita in modo reattivo, pertanto mantenendo il sistema operativo flessibile ed efficiente anche.

Esistono diversi metodi sul delegato Provider (`CXProviderDelegate`) che deve essere sottoposto a override per gestire normalmente anche tali situazioni di Timeout.

### <a name="system-restrictions"></a>Restrizioni di sistema

In base allo stato corrente del dispositivo iOS in esecuzione l'app per iOS 10 VOIP, potrebbero applicate alcune restrizioni del sistema.

Ad esempio, una chiamata VOIP in ingresso può essere limitata dal sistema se:

1. La persona che ha chiamato è sul chiamante bloccati dell'utente.
2. Dispositivo iOS dell'utente si trova nella modalità-non modificare.

Se una chiamata VOIP è limitata da uno qualsiasi di queste situazioni, è possibile utilizzare il codice seguente per risolvere il problema:

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

### <a name="voip-audio"></a>VOIP Audio

CallKit offre numerosi vantaggi per la gestione delle risorse audio che un'app per iOS 10 VOIP richiederanno durante una chiamata VOIP in tempo reale. Uno dei vantaggi principali è l'app sessione audio verrà elevati priorità durante l'esecuzione in iOS 10. Si tratta del livello di priorità stesso come il telefono incorporata e App FaceTime e questo livello di priorità avanzata impedirà le altre applicazioni in esecuzione interrompere la sessione audio VOIP dell'app.

Inoltre, CallKit ha accesso a altri hint di routing audio che possono migliorare le prestazioni e indirizzare in modo intelligente audio VOIP ai dispositivi nell'output specifico durante una chiamata in tempo reale in base alle preferenze dell'utente e stati del dispositivo. Ad esempio, basato su dispositivi collegati, ad esempio cuffie bluetooth, una connessione in tempo reale di CarPlay o le impostazioni di accessibilità.

Durante il ciclo di vita di un tipico VOIP chiamare usando CallKit, l'app sarà necessario configurare il flusso Audio CallKit fornirà il. Esaminare l'esempio seguente:

[![](callkit-images/callkit09.png "La sequenza di azioni di chiamata di Start")](callkit-images/callkit09.png#lightbox)

1. Un'azione di chiamare Start viene ricevuta dall'applicazione per rispondere a una chiamata in ingresso.
2. Prima di questa azione viene soddisfatto dall'app, fornisce la configurazione necessaria per il relativo `AVAudioSession`.
3. L'app indica al sistema che l'azione sia stata completata.
4. Prima che si connette la chiamata, CallKit fornisce ad alta priorità `AVAudioSession` corrispondente alla configurazione che ha richiesto l'app. L'applicazione riceverà una notifica tramite il `DidActivateAudioSession` metodo del relativo `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Utilizzo di estensioni di Directory di chiamata

Quando si lavora con CallKit, _chiamare le estensioni di Directory_ consentono di aggiungere i numeri di chiamate bloccati e identificare i numeri che sono specifici di un'applicazione specificata VOIP ai contatti nell'app contatti nel dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementazione di un'estensione di Directory di chiamata

Per implementare un'estensione di Directory chiamare in un'app xamarin. IOS, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic sul nome della soluzione nel **Esplora** e selezionare **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **chiamare le estensioni di Directory** e fare clic su di **Avanti** pulsante: 

    [![](callkit-images/calldir01.png "Creazione di una nuova estensione Directory chiamare")](callkit-images/calldir01.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic su di **Avanti** pulsante: 

    [![](callkit-images/calldir02.png "Immettere un nome per l'estensione")](callkit-images/calldir02.png#lightbox)
5. Regolare il **nome progetto** e/o **Nome soluzione** se necessario, fare clic su di **crea** pulsante: 

    [![](callkit-images/calldir03.png "Creazione del progetto")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire la soluzione dell'app in Visual Studio.
2. Fare clic sul nome della soluzione nel **Esplora** e selezionare **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **estensioni** > **chiamare le estensioni di Directory** e fare clic su di **Avanti** pulsante: 

    [![](callkit-images/calldir01w.png "Creazione di una nuova estensione Directory chiamare")](callkit-images/calldir01.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic su di **OK** pulsante

-----


Verrà aggiunta una `CallDirectoryHandler.cs` classe al progetto che è simile alla seguente:

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

Il `BeginRequest` metodo nel gestore della Directory di chiamare dovrà essere modificato per fornire la funzionalità richiesta. Nel caso dell'esempio precedente, tenta di impostare l'elenco dei numeri bloccati e disponibili nel database dei contatti dell'app VOIP. Se una richiesta ha esito negativo per qualsiasi motivo, creare un `NSError` descrivere l'errore e viene passato il `CancelRequest` metodo la `CXCallDirectoryExtensionContext` classe.

Per impostare l'utilizzo di numeri bloccato il `AddBlockingEntry` metodo la `CXCallDirectoryExtensionContext` classe. I numeri specificati per il metodo _deve_ in ordine numerico crescente. Per l'utilizzo della memoria e prestazioni ottimali quando sono presenti che molti numeri di telefono, prendere in considerazione solo il caricamento di un subset dei numeri in un determinato momento e utilizzo pool autorelease per rilasciare gli oggetti allocati durante ogni batch di numeri che vengono caricati.

Per segnalare all'app di contattare dei numeri di telefono noti all'app VOIP, utilizzare il `AddIdentificationEntry` metodo la `CXCallDirectoryExtensionContext` classe e specificare il numero e un'etichetta di identificazione. Nuovamente, i numeri specificati per il metodo _deve_ in ordine numerico crescente. Per l'utilizzo della memoria e prestazioni ottimali quando sono presenti che molti numeri di telefono, prendere in considerazione solo il caricamento di un subset dei numeri in un determinato momento e utilizzo pool autorelease per rilasciare gli oggetti allocati durante ogni batch di numeri che vengono caricati.


## <a name="summary"></a>Riepilogo

In questo articolo è descritta la nuova API CallKit che Apple rilasciate in iOS 10 e come implementarlo in App xamarin VOIP. È visualizzato come CallKit consente a un'app integrare il sistema di iOS, come fornisce la parità di funzionalità con App predefinite (ad esempio Phone) e aumenta la visibilità di un'app in iOS nei percorsi, ad esempio il blocco e schermate Home, tramite le interazioni di Siri e tramite le app dei contatti.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
