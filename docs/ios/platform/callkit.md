---
title: CallKit in Xamarin.iOS
description: Questo articolo illustra la nuova API CallKit rilasciata da Apple in iOS 10 e come implementarla nelle app VOIP Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 8f8b92e48578c08e491f92bcc7e2a9add67ee0cd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032606"
---
# <a name="callkit-in-xamarinios"></a>CallKit in Xamarin.iOS

La nuova API CallKit in iOS 10 consente alle app VOIP di integrarsi con l'interfaccia utente di iPhone e fornire all'utente finale un'interfaccia familiare ed esperienza. Con questa API gli utenti possono visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando le visualizzazioni **Preferiti** e **recenti** dell'app per telefoni.

## <a name="about-callkit"></a>Informazioni su CallKit

Secondo Apple, CallKit è un nuovo Framework che consente di elevare le app Voice over IP (VOIP) di terze parti a un'esperienza di prima entità in iOS 10. L'API CallKit consente alle app VOIP di integrarsi con l'interfaccia utente di iPhone e fornire all'utente finale un'interfaccia e un'esperienza note. Proprio come l'App Phone incorporata, un utente può visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando le visualizzazioni **Preferiti** e **recenti** dell'app per telefoni.

Inoltre, l'API di CallKit offre la possibilità di creare estensioni di app che possono associare un numero di telefono a un nome (ID chiamante) o indicare al sistema quando un numero deve essere bloccato (blocco delle chiamate).

### <a name="the-existing-voip-app-experience"></a>Esperienza dell'app VOIP esistente

Prima di illustrare la nuova API CallKit e le relative capacità, è opportuno esaminare l'esperienza utente corrente con un'app VOIP di terze parti in iOS 9 (e meno) usando un'app VOIP fittizia denominata MonkeyCall. MonkeyCall è un'app semplice che consente all'utente di inviare e ricevere chiamate VOIP usando le API iOS esistenti.

Attualmente, se l'utente riceve una chiamata in ingresso in MonkeyCall e il relativo iPhone è bloccato, la notifica ricevuta sulla schermata di blocco non è distinguibile da altri tipi di notifica, ad esempio quelli dei messaggi o delle app di posta elettronica.

Se l'utente vuole rispondere alla chiamata, deve far scorrere la notifica MonkeyCall per aprire l'app e immettere il codice di accesso (o ID tocco utente) per sbloccare il telefono prima di poter accettare la chiamata e avviare la conversazione.

L'esperienza è ugualmente complessa se il telefono è sbloccato. Anche in questo caso, la chiamata MonkeyCall in ingresso viene visualizzata come un banner di notifica standard che scorre nella parte superiore della schermata. Poiché la notifica è temporanea, è possibile che l'utente non sia in grado di aprire il centro notifiche e di trovare la notifica specifica a cui rispondere, quindi chiamare o trovare e avviare manualmente l'app MonkeyCall.

### <a name="the-callkit-voip-app-experience"></a>Esperienza dell'app VOIP CallKit

Implementando le nuove API CallKit nell'app MonkeyCall, l'esperienza dell'utente con una chiamata VOIP in ingresso può essere notevolmente migliorata in iOS 10. Eseguire l'esempio dell'utente che riceve una chiamata VOIP quando il telefono è bloccato da sopra. Implementando CallKit, la chiamata verrà visualizzata nella schermata di blocco di iPhone, così come se la chiamata venisse ricevuta dall'App Phone incorporata, con l'interfaccia utente nativa a schermo intero e con funzionalità di scorrimento standard.

Anche in questo caso, se l'iPhone viene sbloccato quando viene ricevuta una chiamata VOIP di MonkeyCall, vengono presentate le stesse funzionalità a schermo intero, interfaccia utente nativa e con scorrimento a risposta e tocco standard dell'app Phone incorporata e MonkeyCall è in grado di riprodurre una suoneria personalizzata .

CallKit fornisce funzionalità aggiuntive a MonkeyCall, consentendo alle chiamate VOIP di interagire con altri tipi di chiamate, di apparire negli elenchi predefiniti e dei preferiti, per usare le funzionalità predefinite non disturbare e bloccare le funzionalità, avviare chiamate MonkeyCall da Siri e offre la possibilità per gli utenti di assegnare chiamate MonkeyCall a utenti nell'app Contatti.

Le sezioni seguenti illustrano in modo dettagliato l'architettura CallKit, i flussi delle chiamate in ingresso e in uscita e l'API CallKit.

## <a name="the-callkit-architecture"></a>Architettura CallKit

In iOS 10, Apple ha adottato CallKit in tutti i servizi di sistema, in modo che le chiamate effettuate su CarPlay, ad esempio, siano note all'interfaccia utente del sistema tramite CallKit. Nell'esempio riportato di seguito, poiché MonkeyCall adotta CallKit, è noto al sistema in modo analogo a questi servizi di sistema incorporati e ottiene tutte le stesse funzionalità:

[![](callkit-images/callkit01.png "The CallKit Service Stack")](callkit-images/callkit01.png#lightbox)

Esaminare più da vicino l'app MonkeyCall del diagramma precedente. L'app contiene tutto il codice per comunicare con la propria rete e contiene le proprie interfacce utente. Collegamenti in CallKit per comunicare con il sistema:

[![](callkit-images/callkit02.png "MonkeyCall App Architecture")](callkit-images/callkit02.png#lightbox)

Sono disponibili due interfacce principali in CallKit che l'app usa:

- `CXProvider`: consente all'app MonkeyCall di informare il sistema di eventuali notifiche fuori banda che potrebbero verificarsi.
- `CXCallController`: consente all'app MonkeyCall di informare il sistema delle azioni dell'utente locale.

### <a name="the-cxprovider"></a>CXProvider

Come indicato in precedenza, `CXProvider` consente a un'app di informare il sistema di eventuali notifiche fuori banda che potrebbero verificarsi. Si tratta di notifiche che non si verificano a causa di azioni dell'utente locale, ma che si verificano a causa di eventi esterni come le chiamate in ingresso.

Un'app deve usare la `CXProvider` per gli elementi seguenti:

- Segnala una chiamata in ingresso al sistema.
- Segnala un oggetto che la chiamata in uscita è connessa al sistema.
- Segnala l'utente remoto che termina la chiamata al sistema.

Quando l'app vuole comunicare con il sistema, usa la classe `CXCallUpdate` e quando il sistema deve comunicare con l'app, usa la classe `CXAction`:

[![](callkit-images/callkit03.png "Communicating with the system via a CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

Il `CXCallController` consente a un'app di informare il sistema di azioni dell'utente locale, ad esempio l'avvio di una chiamata VOIP da parte dell'utente. Implementando una `CXCallController` l'app entra in gioco con altri tipi di chiamate nel sistema. Se, ad esempio, è già in corso una chiamata di telefonia attiva, `CXCallController` possibile consentire all'app VOIP di inserire la chiamata in attesa e avviare o rispondere a una chiamata VOIP.

Un'app deve usare la `CXCallController` per gli elementi seguenti:

- Segnala quando l'utente ha avviato una chiamata in uscita al sistema.
- Segnala quando l'utente risponde a una chiamata in ingresso al sistema.
- Segnala quando l'utente termina una chiamata al sistema.

Quando l'app desidera comunicare le azioni dell'utente locale al sistema, usa la classe `CXTransaction`:

[![](callkit-images/callkit04.png "Reporting to the system using a CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementazione di CallKit

Le sezioni seguenti illustrano come implementare CallKit in un'app VOIP Xamarin.iOS. Per questo esempio, questo documento utilizzerà il codice dell'app MonkeyCall VOIP fittizia. Il codice presentato in questo argomento rappresenta diverse classi di supporto, le parti specifiche di CallKit sono descritte in dettaglio nelle sezioni seguenti.

### <a name="the-activecall-class"></a>Classe ActiveCall

La classe `ActiveCall` viene usata dall'app MonkeyCall per conservare tutte le informazioni su una chiamata VOIP attualmente attiva come indicato di seguito:

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

`ActiveCall` include diverse proprietà che definiscono lo stato della chiamata e due eventi che possono essere generati quando lo stato di chiamata cambia. Poiché questo è solo un esempio, esistono tre metodi usati per simulare l'avvio, la risposta e la fine di una chiamata.

### <a name="the-startcallrequest-class"></a>Classe StartCallRequest

La `StartCallRequest` classe statica, fornisce alcuni metodi helper che verranno usati quando si lavora con le chiamate in uscita:

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

Le classi `CallHandleFromURL` e `CallHandleFromActivity` vengono usate in AppDelegate per ottenere l'handle di contatto della persona chiamata in una chiamata in uscita. Per ulteriori informazioni, vedere la sezione [gestione delle chiamate in uscita](#handling-outgoing-calls) riportata di seguito.

### <a name="the-activecallmanager-class"></a>Classe ActiveCallManager

La classe `ActiveCallManager` gestisce tutte le chiamate aperte nell'app MonkeyCall.

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

Anche in questo caso, poiché si tratta di una simulazione, il `ActiveCallManager` gestisce solo una raccolta di oggetti `ActiveCall` e ha una routine per trovare una determinata chiamata dalla relativa proprietà `UUID`. Sono inclusi anche i metodi per avviare, terminare e modificare lo stato di una chiamata in uscita. Per ulteriori informazioni, vedere la sezione [gestione delle chiamate in uscita](#handling-outgoing-calls) riportata di seguito.

### <a name="the-providerdelegate-class"></a>Classe ProviderDelegate

Come illustrato in precedenza, un `CXProvider` fornisce la comunicazione bidirezionale tra l'app e il sistema per le notifiche fuori banda. Lo sviluppatore deve fornire un `CXProviderDelegate` personalizzato e collegarlo alla `CXProvider` per consentire all'app di gestire gli eventi CallKit fuori banda. MonkeyCall usa i `CXProviderDelegate`seguenti:

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

Quando viene creata un'istanza di questo delegato, viene passata la `ActiveCallManager` che utilizzerà per gestire qualsiasi attività chiamata. Definisce quindi i tipi di handle (`CXHandleType`) a cui il `CXProvider` risponderà:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

E ottiene l'immagine modello che verrà applicata all'icona dell'app quando è in corso una chiamata:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Questi valori vengono aggregati in un `CXProviderConfiguration` che verrà usato per configurare l'`CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

Il delegato crea quindi un nuovo `CXProvider` con queste configurazioni e vi si connette:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Quando si usa CallKit, l'app non creerà più e gestirà le proprie sessioni audio, ma sarà necessario configurare e usare una sessione audio che verrà creata e gestita dal sistema. 

Se si tratta di un'app reale, viene usato il metodo `DidActivateAudioSession` per avviare la chiamata con una `AVAudioSession` preconfigurata fornita dal sistema:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

USA anche il metodo `DidDeactivateAudioSession` per finalizzare e rilasciare la connessione alla sessione audio fornita dal sistema:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

Il resto del codice verrà illustrato in dettaglio nelle sezioni che seguono.

### <a name="the-appdelegate-class"></a>Classe AppDelegate

MonkeyCall usa il AppDelegate per contenere le istanze del `ActiveCallManager` e `CXProviderDelegate` che verranno usate nell'app:

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

I metodi di override `OpenUrl` e `ContinueUserActivity` vengono usati quando l'app elabora una chiamata in uscita. Per ulteriori informazioni, vedere la sezione [gestione delle chiamate in uscita](#handling-outgoing-calls) riportata di seguito.

## <a name="handling-incoming-calls"></a>Gestione delle chiamate in ingresso

Ci sono diversi Stati e processi che una chiamata VOIP in ingresso può attraversare durante un flusso di lavoro di chiamata in ingresso tipico, ad esempio:

- Informare l'utente (e il sistema) che esiste una chiamata in ingresso.
- Ricezione di una notifica quando l'utente desidera rispondere alla chiamata e inizializzare la chiamata con l'altro utente.
- Informare il sistema e la rete di comunicazione quando l'utente desidera terminare la chiamata corrente.

Le sezioni seguenti descrivono in dettaglio il modo in cui un'app può usare CallKit per gestire il flusso di lavoro delle chiamate in ingresso, usando di nuovo l'app MonkeyCall VOIP come esempio.

### <a name="informing-user-of-incoming-call"></a>Informare l'utente della chiamata in ingresso

Quando un utente remoto ha avviato una conversazione VOIP con l'utente locale, si verifica quanto segue:

[![](callkit-images/callkit05.png "A remote user has started a VOIP conversation")](callkit-images/callkit05.png#lightbox)

1. L'app riceve una notifica dalla rete di comunicazione in cui è presente una chiamata VOIP in ingresso.
2. L'app usa il `CXProvider` per inviare un `CXCallUpdate` al sistema per informarlo della chiamata.
3. Il sistema pubblica la chiamata all'interfaccia utente del sistema, ai servizi di sistema e a qualsiasi altra app VOIP usando CallKit.

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

Questo codice crea una nuova istanza di `CXCallUpdate` e ne connette un handle che identificherà il chiamante. USA quindi il metodo `ReportNewIncomingCall` della classe `CXProvider` per informare il sistema della chiamata. Se l'operazione ha esito positivo, la chiamata viene aggiunta alla raccolta di chiamate attive dell'app, in caso contrario, è necessario segnalare l'errore all'utente.

### <a name="user-answering-incoming-call"></a>Chiamata in ingresso per la risposta dell'utente

Se l'utente desidera rispondere alla chiamata VOIP in ingresso, si verifica quanto segue:

[![](callkit-images/callkit06.png "The user answers the incoming VOIP call")](callkit-images/callkit06.png#lightbox)

1. L'interfaccia utente del sistema informa il sistema che l'utente desidera rispondere alla chiamata VOIP.
2. Il sistema invia un `CXAnswerCallAction` al `CXProvider` dell'app per informarlo della finalità della risposta.
3. L'app informa la rete di comunicazione che l'utente risponde alla chiamata e la chiamata VOIP continua come di consueto.

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

Questo codice cerca innanzitutto la chiamata specificata nell'elenco delle chiamate attive. Se la chiamata non viene trovata, il sistema riceve una notifica e il metodo viene chiuso. Se viene trovato, viene chiamato il metodo `AnswerCall` della classe `ActiveCall` per avviare la chiamata e il sistema è in grado di ottenere informazioni in caso di esito positivo o negativo.

### <a name="user-ending-incoming-call"></a>Chiamata in entrata finale dell'utente

Se l'utente desidera terminare la chiamata all'interno dell'interfaccia utente dell'app, si verifica quanto segue:

[![](callkit-images/callkit07.png "The user terminates the call from within the app's UI")](callkit-images/callkit07.png#lightbox)

1. L'app crea `CXEndCallAction` che vengono aggregate in un `CXTransaction` inviato al sistema per indicare che la chiamata è terminata.
2. Il sistema verifica la finalità della chiamata End e invia il `CXEndCallAction` all'app tramite il `CXProvider`.
3. L'app informa quindi la rete di comunicazione che la chiamata sta terminando.

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

Questo codice cerca innanzitutto la chiamata specificata nell'elenco delle chiamate attive. Se la chiamata non viene trovata, il sistema riceve una notifica e il metodo viene chiuso. Se viene trovato, viene chiamato il metodo `EndCall` della classe `ActiveCall` per terminare la chiamata e il sistema è in grado di ottenere informazioni in caso di esito positivo o negativo. Se ha esito positivo, la chiamata viene rimossa dalla raccolta di chiamate attive.

## <a name="managing-multiple-calls"></a>Gestione di più chiamate

La maggior parte delle app VOIP è in grado di gestire più chiamate contemporaneamente. Ad esempio, se è attualmente presente una chiamata VOIP attiva e l'app riceve una notifica che a è presente una nuova chiamata in ingresso, l'utente può sospendere o bloccare la prima chiamata per rispondere al secondo.

Nella situazione precedente, il sistema invierà un `CXTransaction` all'app che includerà un elenco di più azioni, ad esempio `CXEndCallAction` e `CXAnswerCallAction`. Tutte queste azioni devono essere soddisfatte singolarmente, in modo che il sistema possa aggiornare l'interfaccia utente in modo appropriato.

## <a name="handling-outgoing-calls"></a>Gestione delle chiamate in uscita

Se l'utente tocca una voce dall'elenco degli ultimi (nell'app per il telefono), ad esempio da una chiamata che appartiene all'app, verrà inviata una _richiesta di avvio_ dal sistema:

[![](callkit-images/callkit08.png "Receiving a Start Call Intent")](callkit-images/callkit08.png#lightbox)

1. L'app creerà un' _azione Avvia chiamata_ in base alla finalità della chiamata iniziale ricevuta dal sistema. 
2. L'App utilizzerà il `CXCallController` per richiedere l'azione di avvio della chiamata dal sistema.
3. Se il sistema accetta l'azione, verrà restituita all'app tramite il delegato `XCProvider`.
4. L'app avvia la chiamata in uscita con la rete di comunicazione.

Per ulteriori informazioni sugli Intent, vedere la documentazione relativa alle estensioni per gli Intent [e le estensioni dell'interfaccia utente](~/ios/platform/sirikit/understanding-sirikit.md) . 

### <a name="the-outgoing-call-lifecycle"></a>Ciclo di vita delle chiamate in uscita

Quando si lavora con CallKit e una chiamata in uscita, l'app deve informare il sistema dei seguenti eventi del ciclo di vita:

1. **Avvio** -informa il sistema che sta per essere avviata una chiamata in uscita.
2. **Started** : informa il sistema che è stata avviata una chiamata in uscita.
3. **Connessione** : informa il sistema che la chiamata in uscita è connessa.
4. **Connected** : informa che la chiamata in uscita è connessa e che entrambe le parti possono comunicare ora.

Il codice seguente, ad esempio, avvierà una chiamata in uscita:

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

Crea una `CXHandle` e la usa per configurare una `CXStartCallAction` che viene inclusa in un `CXTransaction` inviato al sistema usando il metodo `RequestTransaction` della classe `CXCallController`. Chiamando il metodo `RequestTransaction`, il sistema può inserire eventuali chiamate esistenti, indipendentemente dall'origine (app per telefono, FaceTime, VOIP e così via), prima che venga avviata la nuova chiamata.

La richiesta di avvio di una chiamata VOIP in uscita può provenire da diverse origini, ad esempio Siri, una voce in una scheda contatto (nell'app Contatti) o dall'elenco dei recenti (nell'app per telefoni). In queste situazioni, all'app verrà inviato uno scopo di chiamata di inizio all'interno di una `NSUserActivity` e il AppDelegate dovrà gestirlo:

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

Qui viene usato il metodo `CallHandleFromActivity` della classe helper `StartCallRequest` per ottenere l'handle per la persona chiamata (vedere [la classe StartCallRequest](#the-startcallrequest-class) precedente).

Il metodo `PerformStartCallAction` della [classe ProviderDelegate](#the-providerdelegate-class) viene usato per avviare la chiamata in uscita effettiva e informare il sistema del ciclo di vita:

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

Crea un'istanza della classe `ActiveCall` (per conservare le informazioni sulla chiamata in corso) e popola con la persona chiamata. Gli eventi `StartingConnectionChanged` e `ConnectedChanged` vengono utilizzati per monitorare e segnalare il ciclo di vita delle chiamate in uscita. La chiamata viene avviata e il sistema informa che l'azione è stata soddisfatta.

### <a name="ending-an-outgoing-call"></a>Terminazione di una chiamata in uscita

Quando l'utente ha terminato la chiamata in uscita e desidera terminarlo, è possibile usare il codice seguente:

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

Se crea un `CXEndCallAction` con l'UUID della chiamata a end, lo aggrega in un `CXTransaction` inviato al sistema usando il metodo `RequestTransaction` della classe `CXCallController`. 

## <a name="additional-callkit-details"></a>Ulteriori dettagli su CallKit

Questa sezione illustra alcuni dettagli aggiuntivi che lo sviluppatore deve prendere in considerazione quando si lavora con CallKit, ad esempio:

- Configurazione provider
- Errori di azione
- Restrizioni di sistema
- Audio VOIP

### <a name="provider-configuration"></a>Configurazione provider

La configurazione del provider consente a un'app VOIP iOS 10 di personalizzare l'esperienza utente (all'interno dell'interfaccia utente nativa in-Call) quando si lavora con CallKit.

Un'app può creare i tipi di personalizzazione seguenti:

- Visualizza un nome localizzato.
- Abilitare il supporto per le chiamate video.
- Personalizzare i pulsanti nell'interfaccia utente in-Call presentando l'icona dell'immagine modello. L'interazione dell'utente con i pulsanti personalizzati viene inviata direttamente all'app per l'elaborazione. 

### <a name="action-errors"></a>Errori di azione

le app VOIP iOS 10 che usano CallKit devono gestire correttamente le azioni in modo non corretto e mantengono l'utente informato sempre sullo stato dell'azione. 

Prendere in considerazione l'esempio seguente:

1. L'app ha ricevuto un'azione di chiamata iniziale e ha iniziato il processo di inizializzazione di una nuova chiamata VOIP con la rete di comunicazione.
2. A causa della capacità di comunicazione di rete limitata o nessuna, la connessione non riesce.
3. L'app *deve* inviare di nuovo il messaggio di **errore** all'azione Avvia chiamata (`Action.Fail()`) per informare il sistema dell'errore.
4. Ciò consente al sistema di informare l'utente dello stato della chiamata. Ad esempio, per visualizzare l'interfaccia utente di chiamata non riuscita.

Inoltre, un'app VOIP iOS 10 dovrà rispondere agli errori di _timeout_ che possono verificarsi quando un'azione prevista non può essere elaborata entro un determinato periodo di tempo. A ogni tipo di azione fornito da CallKit è associato un valore di timeout massimo. Questi valori di timeout assicurano che qualsiasi azione CallKit richiesta dall'utente venga gestita in modo reattivo, mantenendo al tempo stesso il fluido del sistema operativo e la reattività.

Esistono diversi metodi sul delegato del provider (`CXProviderDelegate`) che devono essere sottoposti a override per gestire correttamente anche questa situazione di timeout.

### <a name="system-restrictions"></a>Restrizioni di sistema

In base allo stato corrente del dispositivo iOS che esegue l'app VOIP iOS 10, è possibile che vengano applicate alcune restrizioni di sistema.

Ad esempio, una chiamata VOIP in ingresso può essere limitata dal sistema se:

1. La persona che chiama è nell'elenco dei chiamanti bloccati dell'utente.
2. Il dispositivo iOS dell'utente si trova in modalità non di disturbo.

Se una chiamata VOIP è limitata da una di queste situazioni, usare il codice seguente per gestirla:

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

CallKit offre diversi vantaggi per la gestione delle risorse audio che dovranno essere richieste da un'app VOIP iOS 10 durante una chiamata VOIP in tempo reale. Uno dei vantaggi principali è la sessione audio dell'app che avrà priorità elevate durante l'esecuzione in iOS 10. Si tratta dello stesso livello di priorità delle app predefinite per telefoni e FaceTime e questo livello di priorità avanzato impedisce ad altre app in esecuzione di interrompere la sessione audio dell'app VOIP.

Inoltre, CallKit ha accesso ad altri hint di routing audio che possono migliorare le prestazioni e instradare in modo intelligente l'audio VOIP a dispositivi di output specifici durante una chiamata Live in base alle preferenze dell'utente e agli Stati dei dispositivi. Ad esempio, in base a dispositivi collegati, ad esempio cuffie Bluetooth, una connessione Live CarPlay o impostazioni di accessibilità.

Durante il ciclo di vita di una chiamata VOIP tipica che usa CallKit, l'app dovrà configurare il flusso audio che CallKit lo fornirà. Esaminare l'esempio seguente:

[![](callkit-images/callkit09.png "The Start Call Action Sequence")](callkit-images/callkit09.png#lightbox)

1. Un'azione Avvia chiamata viene ricevuta dall'app per rispondere a una chiamata in ingresso.
2. Prima che questa azione venga soddisfatta dall'app, fornisce la configurazione necessaria per la `AVAudioSession`.
3. L'app informa il sistema che l'azione è stata soddisfatta.
4. Prima di connettere la chiamata, CallKit fornisce un `AVAudioSession` ad alta priorità che corrisponde alla configurazione richiesta dall'app. L'app riceverà una notifica tramite il metodo `DidActivateAudioSession` del relativo `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Utilizzo delle estensioni della directory di chiamata

Quando si usa CallKit, le _estensioni della directory di chiamata_ consentono di aggiungere numeri di chiamata bloccati e di identificare i numeri specifici di una determinata app VoIP per i contatti nell'app di contatto sul dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementazione di un'estensione di directory di chiamata

Per implementare un'estensione di directory di chiamata in un'app Xamarin.iOS, seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione dell'app in Visual Studio per Mac.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **Extensions** > **chiamare le estensioni della directory** e fare clic sul pulsante **Avanti** : 

    [![](callkit-images/calldir01.png "Creating a new Call Directory Extension")](callkit-images/calldir01.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic sul pulsante **Avanti** : 

    [![](callkit-images/calldir02.png "Entering a name for the extension")](callkit-images/calldir02.png#lightbox)
5. Modificare il **nome del progetto** e/o il **nome della soluzione** , se necessario, e fare clic sul pulsante **Crea** : 

    [![](callkit-images/calldir03.png "Creating the project")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione dell'app in Visual Studio.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi** > **Aggiungi nuovo progetto**.
3. Selezionare **iOS** > **Extensions** > **chiamare le estensioni della directory** e fare clic sul pulsante **Avanti** : 

    [![](callkit-images/calldir01w.png "Creating a new Call Directory Extension")](callkit-images/calldir01.png#lightbox)
4. Immettere un **nome** per l'estensione e fare clic sul pulsante **OK**

-----

Verrà aggiunta una classe `CallDirectoryHandler.cs` al progetto simile al seguente:

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

Per fornire la funzionalità necessaria è necessario modificare il metodo `BeginRequest` nel gestore della directory di chiamata. Nel caso dell'esempio precedente, tenta di impostare l'elenco dei numeri bloccati e disponibili nel database dei contatti dell'app VOIP. Se una delle due richieste ha esito negativo per qualsiasi motivo, creare un `NSError` per descrivere l'errore e passargli il metodo `CancelRequest` della classe `CXCallDirectoryExtensionContext`.

Per impostare i numeri bloccati, utilizzare il metodo `AddBlockingEntry` della classe `CXCallDirectoryExtensionContext`. I numeri forniti al metodo _devono_ essere in ordine crescente. Per ottimizzare le prestazioni e l'utilizzo della memoria quando sono presenti molti numeri di telefono, prendere in considerazione solo il caricamento di un subset di numeri in un determinato momento e l'uso di pool di versioni rilasciati per rilasciare gli oggetti allocati durante ogni batch di numeri caricati.

Per informare di contattare l'app dei numeri di contatto noti all'app VOIP, usare il metodo `AddIdentificationEntry` della classe `CXCallDirectoryExtensionContext` e specificare sia il numero che l'etichetta di identificazione. Anche in questo caso, i numeri forniti al metodo _devono_ essere in ordine crescente. Per ottimizzare le prestazioni e l'utilizzo della memoria quando sono presenti molti numeri di telefono, prendere in considerazione solo il caricamento di un subset di numeri in un determinato momento e l'uso di pool di versioni rilasciati per rilasciare gli oggetti allocati durante ogni batch di numeri caricati.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la nuova API CallKit rilasciata da Apple in iOS 10 e come implementarla nelle app VOIP di Xamarin.iOS. Ha illustrato come CallKit consente a un'app di integrarsi nel sistema iOS, come fornisce la parità di funzionalità con le app predefinite (ad esempio, il telefono) e come aumenta la visibilità di un'app in tutti i dispositivi iOS in posizioni quali le schermate di blocco e Home, tramite le interazioni Siri e via app dei contatti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
