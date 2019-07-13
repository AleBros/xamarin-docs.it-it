---
title: Riconoscimento vocale in xamarin. IOS
description: Questo articolo viene presentata la nuova API di riconoscimento vocale e viene illustrato come implementare questo metodo in un'app xamarin. IOS per supportare continua riconoscimento vocale e trascrizione vocale (dal vivi o registrati flussi audio) nel testo.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 8af7474036eb0fd6e2236cf52e96b8d12c8bc44e
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865712"
---
# <a name="speech-recognition-in-xamarinios"></a>Riconoscimento vocale in xamarin. IOS

_Questo articolo viene presentata la nuova API di riconoscimento vocale e viene illustrato come implementare questo metodo in un'app xamarin. IOS per supportare continua riconoscimento vocale e trascrizione vocale (dal vivi o registrati flussi audio) nel testo._

Nuovo ai dispositivi iOS 10, Apple ha di rilasciare l'API riconoscimento vocale che consente a un'app iOS supportare continua riconoscimento vocale e trascrizione vocale (dal vivi o registrati flussi audio) nel testo.

In base a Apple, l'API riconoscimento vocale ha le funzionalità e i vantaggi seguenti:

- Altamente accurati
- Stato dell'arte
- Facile da usare
- Fast
- Supporta più linguaggi
- Privacy dell'utente per aspetti

## <a name="how-speech-recognition-works"></a>Funzionamento del riconoscimento vocale

Riconoscimento vocale viene implementato in un'app per iOS, l'acquisizione audio in tempo reale o pre-registrato (in una delle lingue vocali che supporta l'API) e passando a un sistema di riconoscimento vocale che restituisce una trascrizione di testo normale delle vocali.

[![](speech-images/speech01.png "Funzionamento del riconoscimento vocale")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dettatura tramite tastiera

Quando la maggior parte degli utenti considera il riconoscimento vocale in un dispositivo iOS, pensano dell'Assistente vocale Siri predefinito che è stato rilasciato con la dettatura tramite tastiera in iOS 5, con l'iPhone 4S.

La dettatura tramite tastiera è supportata da qualsiasi elemento dell'interfaccia che supporta TextKit (ad esempio `UITextField` o `UITextArea`) e viene attivato dall'utente facendo clic sul pulsante dettatura (direttamente a sinistra della barra spaziatrice) della tastiera virtuale iOS.

Apple ha rilasciato le statistiche la dettatura tramite tastiera seguenti (raccolte dal 2011):

- La dettatura tramite tastiera è stato ampiamente utilizzata dopo il rilascio in iOS 5.
- Circa 65.000 App usano al giorno.
- Su un terzo del tutto iOS dettatura viene eseguita in un'app di terze parti 3rd.

La dettatura tramite tastiera è estremamente facile da usare quando è non necessario alcun intervento da parte dello sviluppatore, diversi dall'uso di un elemento dell'interfaccia TextKit nella progettazione dell'interfaccia utente dell'app. La dettatura tramite tastiera ha anche il vantaggio di non richiedere eventuali richieste di privilegi speciali dall'app prima che possa essere utilizzato.

Le app che usano le nuove API di riconoscimento vocale richiedono autorizzazioni speciali per essere concesso dall'utente, poiché il riconoscimento vocale di richiede la trasmissione e l'archiviazione temporanea dei dati nei server di Apple. Vedere la [miglioramenti della protezione e Privacy](~/ios/app-fundamentals/security-privacy.md) documentazione per informazioni dettagliate.

Mentre la dettatura tramite tastiera è facile da implementare, dotati diverse limitazioni e gli svantaggi:

- Richiede l'uso di un campo di Input di testo e la visualizzazione di una tastiera.
- Funziona con input solo audio in tempo reale e l'app non ha alcun controllo sul processo di registrazione audio.
- Fornisce alcun controllo rispetto al linguaggio utilizzato per interpretare vocale dell'utente.
- Non è possibile per le app di sapere se il pulsante di dettatura è ancora disponibile per l'utente.
- L'app non è possibile personalizzare il processo di registrazione audio.
- Fornisce un set di risultati molto shallow che non dispone di informazioni, ad esempio temporizzazione e di confidenza.

### <a name="speech-recognition-api"></a>API riconoscimento vocale

Nuovo ai dispositivi iOS 10, Apple ha rilasciato l'API riconoscimento vocale che offre un modo più potente per un'app per iOS implementare il riconoscimento vocale. Questa API corrisponde a quello utilizzato per potenziare Siri sia la dettatura tramite tastiera Apple ed è in grado di fornire la trascrizione veloce con precisione lo stato dell'arte.

I risultati restituiti dall'API di riconoscimento vocale in modo trasparente personalizzati a utenti singoli, senza che l'app dover raccogliere o accedere ai dati personali dell'utente.

L'API riconoscimento vocale fornisce risultati all'app per la chiamata in tempo quasi reale quando l'utente sta parlando e fornisce altre informazioni sui risultati di una traduzione di solo testo. Sono inclusi:

- Più interpretazioni su ciò che dice che l'utente.
- Livelli di confidenza per le singole traduzioni.
- Informazioni di temporizzazione.

Come descritto in precedenza, l'audio per la conversione può essere fornito sia da un feed in tempo reale, o da origine pre-registrato e in tutte le lingue regionali supportate da iOS 10 e oltre 50 lingue.

L'API riconoscimento vocale può essere usato su qualsiasi dispositivo iOS che eseguono iOS 10 e nella maggior parte dei casi, richiede una connessione internet dinamica poiché la maggior parte delle conversioni ha luogo nei server di Apple. Ciò premesso, alcuni dispositivi supportano always on per iOS più recenti, sul dispositivo traduzione dei linguaggi specifici del dominio.

Apple ha incluso un'API di disponibilità per determinare se una determinata lingua è disponibile per la conversione nel momento attuale. L'app deve usare questa API anziché eseguire direttamente il test per la connettività internet se stesso.

Come indicato in precedenza nella sezione la dettatura tramite tastiera, riconoscimento vocale di richiede la trasmissione e l'archiviazione temporanea dei dati nei server di Apple tramite internet e pertanto, l'app _necessario_ richiesta l'autorizzazione dell'utente per l'esecuzione riconoscimento includendo il `NSSpeechRecognitionUsageDescription` chiave nel relativo `Info.plist` file e la chiamata di `SFSpeechRecognizer.RequestAuthorization` (metodo). 

In base all'origine dell'audio in uso per il riconoscimento vocale, apportare altre modifiche all'app `Info.plist` file potrebbe essere necessario. Vedere la [miglioramenti della protezione e Privacy](~/ios/app-fundamentals/security-privacy.md) documentazione per informazioni dettagliate.

## <a name="adopting-speech-recognition-in-an-app"></a>Adozione di riconoscimento vocale in un'App

Esistono quattro passaggi principali che lo sviluppatore deve intraprendere per adottare il riconoscimento vocale in un'app per iOS:

- Fornire una descrizione dell'utilizzo dell'app `Info.plist` file utilizzando il `NSSpeechRecognitionUsageDescription` chiave. Ad esempio, un'app della fotocamera potrebbe includere la descrizione seguente, _"In questo modo è possibile scattare una foto semplicemente specificando la parola 'formaggio'."_
- Richiedere l'autorizzazione chiamando il `SFSpeechRecognizer.RequestAuthorization` metodo per presentare una spiegazione (forniti nel `NSSpeechRecognitionUsageDescription` chiave precedente) del motivo per cui l'app intende vocale riconoscimento per l'utente in una finestra di dialogo di accesso e consentire loro di accettare o rifiutare.
- Creare una richiesta di riconoscimento vocale:
    * Per pre-registrato audio su disco, usare il `SFSpeechURLRecognitionRequest` classe.
    * Per l'audio in tempo reale o audio dalla memoria, usare il `SFSPeechAudioBufferRecognitionRequest` classe.
- Passare la richiesta di riconoscimento vocale per un riconoscimento vocale (`SFSpeechRecognizer`) per avviare il riconoscimento. L'app può contenere facoltativamente in restituito `SFSpeechRecognitionTask` per monitorare e tenere traccia dei risultati di riconoscimento.

Questi passaggi verranno trattati in dettaglio di seguito.

### <a name="providing-a-usage-description"></a>Fornire una descrizione dell'utilizzo

Per fornire la necessaria `NSSpeechRecognitionUsageDescription` chiavi nel `Info.plist` file, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic il `Info.plist` file per aprirlo e modificarlo.
2. Passare al **origine** Vista: 

    [![](speech-images/speech02.png "La visualizzazione origine")](speech-images/speech02.png#lightbox)
3. Fare clic su **aggiungere una nuova voce**, immettere `NSSpeechRecognitionUsageDescription` per il **proprietà**, `String` per il **tipo** e un **descrizione utilizzo** come le **valore**. Ad esempio: 

    [![](speech-images/speech03.png "Aggiunta di NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Se l'applicazione gestirà la trascrizione audio in tempo reale, sarà inoltre necessario specificare una descrizione utilizzo microfono. Fare clic su **aggiungere una nuova voce**, immettere `NSMicrophoneUsageDescription` per il **proprietà**, `String` per il **tipo** e un **descrizione utilizzo** come le **valore**. Ad esempio: 

    [![](speech-images/speech04.png "Aggiunta di NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
5. Salvare le modifiche apportate al file.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic il `Info.plist` file per aprirlo e modificarlo.
2. Fare clic su **aggiungere una nuova voce**, immettere `NSSpeechRecognitionUsageDescription` per il **proprietà**, `String` per il **tipo** e un **descrizione utilizzo** come le **valore**. Ad esempio: 

    [![](speech-images/speech03w.png "Aggiunta di NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
3. Se l'applicazione gestirà la trascrizione audio in tempo reale, sarà inoltre necessario specificare una descrizione utilizzo microfono. Fare clic su **aggiungere una nuova voce**, immettere `NSMicrophoneUsageDescription` per il **proprietà**, `String` per il **tipo** e un **descrizione utilizzo** come le **valore**. Ad esempio: 

    [![](speech-images/speech04w.png "Aggiunta di NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Salvare le modifiche apportate al file.

-----

> [!IMPORTANT]
> Impossibile specificare elementi indicati `Info.plist` chiavi (`NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription`) può comportare l'app non superati senza alcun avviso durante il tentativo di accesso di riconoscimento vocale o al microfono per l'audio in tempo reale.




### <a name="requesting-authorization"></a>La richiesta di autorizzazione

Per richiedere l'autorizzazione di utenti obbligatorio che consente all'app di accedere il riconoscimento vocale, modificare la classe di Controller di visualizzazione principale e aggiungere il codice seguente:

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

Il `RequestAuthorization` metodo del `SFSpeechRecognizer` classe richiederà l'autorizzazione da parte dell'utente per il riconoscimento vocale accesso utilizzando il motivo per cui lo sviluppatore fornito nel `NSSpeechRecognitionUsageDescription` chiave del `Info.plist` file.

Oggetto `SFSpeechRecognizerAuthorizationStatus` risultato viene restituito per il `RequestAuthorization` routine di callback del metodo che può essere usato per eseguire azioni in base l'autorizzazione dell'utente. 

> [!IMPORTANT]
> In attesa fino a quando l'utente ha avviato un'azione nell'app che richiede il riconoscimento vocale prima di richiedere l'autorizzazione suggerite da Apple.

### <a name="recognizing-pre-recorded-speech"></a>Riconoscimento vocale pre-registrati

Se l'app è richiesto il riconoscimento vocale da un file WAV o MP3 pre-registrato, è possibile usare il codice seguente:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

Esaminando questo codice in modo dettagliato, prima di tutto, tenta di creare un sistema di riconoscimento vocale (`SFSpeechRecognizer`). Se la lingua predefinita non è supportata per il riconoscimento vocale, `null` viene restituito e si chiude le funzioni.

Se il riconoscimento vocale è disponibile per la lingua predefinita, l'app controlla per verificare se è attualmente disponibile per l'utilizzo di riconoscimento di `Available` proprietà. Ad esempio, il riconoscimento potrebbe non essere disponibile se il dispositivo non ha una connessione internet attiva.

Oggetto `SFSpeechUrlRecognitionRequest` creata dal `NSUrl` percorso del file pre-registrato nel dispositivo iOS e è stato affidato al riconoscimento vocale per l'elaborazione con una routine di callback.

Quando viene chiamato il callback, se il `NSError` non è `null` si è verificato un errore che deve essere gestito. Poiché il riconoscimento vocale viene eseguito in modo incrementale, la routine di callback può essere chiamata più di una sola volta in modo che il `SFSpeechRecognitionResult.Final` proprietà viene testata per verificare se la conversione viene completata e viene scritto versione migliore della traduzione (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Riconoscimento vocale in tempo reale

Se l'app è richiesto il riconoscimento vocale in tempo reale, il processo è molto simile al riconoscimento vocale pre-registrati. Ad esempio:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

Esaminando questo codice in modo dettagliato, crea diverse variabili private per gestire il processo di riconoscimento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

AV Foundation Usa per registrare l'audio che verrà passato a un `SFSpeechAudioBufferRecognitionRequest` per gestire la richiesta di riconoscimento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

L'app prova ad avviare la registrazione e gli eventuali errori vengono gestiti se non è possibile avviare la registrazione:

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

L'attività di riconoscimento viene avviata e viene mantenuto un handle per l'attività di riconoscimento (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

La richiamata viene utilizzata in modo analogo a quello usato in precedenza il riconoscimento vocale pre-registrati.

Se la registrazione è più dall'utente, vengono informati che il motore di Audio e la richiesta di riconoscimento vocale:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Se l'utente annulla l'operazione di riconoscimento, quindi il motore di Audio e attività di riconoscimento deve esserne informato:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

È importante chiamare `RecognitionTask.Cancel` se l'utente annulla la traduzione per liberare memoria e processore del dispositivo.

> [!IMPORTANT]
> Incapacità di fornire il `NSSpeechRecognitionUsageDescription` oppure `NSMicrophoneUsageDescription` `Info.plist` chiavi possono comportare l'app non superati senza alcun avviso durante il tentativo di accesso di riconoscimento vocale o al microfono per l'audio in tempo reale (`var node = AudioEngine.InputNode;`). Vedere le **che fornisce una descrizione dell'utilizzo** sezione precedente per altre informazioni.

## <a name="speech-recognition-limits"></a>Limiti di riconoscimento vocale

Quando si lavora sul riconoscimento vocale in un'app per iOS, Apple impone le limitazioni seguenti:

- Riconoscimento vocale è libero di tutte le app, ma l'utilizzo non è un numero illimitato:
    - I dispositivi iOS singole hanno un numero limitato di riconoscimenti che possono essere eseguite al giorno.
    - Le app saranno limitate a livello globale in base a una richiesta al giorno.
- L'app deve essere preparato per la gestione connessione di rete di riconoscimento vocale e gli errori di limite di utilizzo frequenza.
- Riconoscimento vocale può avere un costo elevato in della batteria e il traffico di rete elevata sul dispositivo iOS dell'utente, per questo motivo, Apple impone un limite di durata audio strict di circa un minuto di riconoscimento vocale max.

Se un'app viene regolarmente raggiungono i limiti massimi per la limitazione della frequenza, Apple richiede che lo sviluppatore di contattarli.

## <a name="privacy-and-usability-considerations"></a>Privacy e considerazioni sulla facilità di utilizzo

Apple ha il suggerimento seguente per diventa trasparente e il rispettando la privacy dell'utente quando si include il riconoscimento vocale in un'app per iOS:

- Durante la registrazione vocale dell'utente, assicurarsi di indicare chiaramente che la registrazione viene eseguita nell'interfaccia utente dell'app. Ad esempio, l'app potrebbe riprodurre un suono un "registrazione" e visualizzare un indicatore di registrazione.
- Non usare il riconoscimento vocale per le informazioni riservate dell'utente, ad esempio password, i dati di integrità o le informazioni finanziarie.
- Mostra i risultati del riconoscimento _prima di_ che agisce su di essi. Ciò non solo fornisce feedback sulla ciò che l'app, infatti, ma consente all'utente di gestire gli errori di riconoscimento non appena vengono eseguite.

## <a name="summary"></a>Riepilogo

Questo articolo è presentata la nuova API di riconoscimento vocale ed è stato illustrato come implementare questo metodo in un'app xamarin. IOS per supportare continua riconoscimento vocale e trascrizione vocale (dal vivi o registrati flussi audio) nel testo. 



## <a name="related-links"></a>Collegamenti correlati

- [SpeakToMe (esempio)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
