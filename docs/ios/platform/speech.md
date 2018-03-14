---
title: Riconoscimento vocale
description: In questo articolo presenta la nuova API di riconoscimento vocale e Mostra come implementarlo in un'app xamarin per supportare il riconoscimento vocale continua e trascrivere vocale (da flussi audio in tempo reale o registrati) nel testo.
ms.topic: article
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 33e27043c3738c5213b17786e5a88fb30a7fc017
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="speech-recognition"></a>Riconoscimento vocale

_In questo articolo presenta la nuova API di riconoscimento vocale e Mostra come implementarlo in un'app xamarin per supportare il riconoscimento vocale continua e trascrivere vocale (da flussi audio in tempo reale o registrati) nel testo._

Nuovo in iOS 10, Apple ha versione API riconoscimento vocale che consente a un'app iOS supportare il riconoscimento vocale continua e trascrivere vocale (da flussi audio in tempo reale o registrati) nel testo.

In base a Apple, l'API di riconoscimento vocale ha le funzionalità e i vantaggi seguenti:

- Accurate
- All'avanguardia
- Facile da usare
- Fast
- Supporta più lingue
- Gli aspetti Privacy dell'utente

## <a name="how-speech-recognition-works"></a>Funzionamento di riconoscimento vocale

Il riconoscimento vocale viene implementato in un'app iOS per l'acquisizione audio preregistrato o in tempo reale (in qualsiasi lingua parlata che supporta l'API) e passato a un riconoscimento vocale che restituisce una trascrizione testo normale dei dialoghi.

[![](speech-images/speech01.png "Funzionamento di riconoscimento vocale")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Tastiera dettatura

La maggior parte degli utenti ritiene del riconoscimento vocale in un dispositivo iOS, ritengono dell'Assistente vocale Siri incorporato rilasciata insieme dettatura tastiera in iOS 5 con 4S l'iPhone.

Tastiera dettatura è supportata da qualsiasi elemento dell'interfaccia che supporta TextKit (ad esempio `UITextField` o `UITextArea`) e viene attivato dall'utente facendo clic sul pulsante dettatura (a sinistra della barra spaziatrice) della tastiera virtuale iOS.

Apple ha rilasciato le statistiche di dettatura tastiera seguenti (raccolte dal 2011):

- Dettatura di tasti è stata ampiamente utilizzata dopo il rilascio in iOS 5.
- Circa 65.000 App usano al giorno.
- Su un terzo di iOS tutti dettatura viene eseguita in un'app di terze parti 3rd.

Tastiera dettatura è estremamente facile da utilizzare come non richiede alcun intervento da parte dello sviluppatore, diverso dall'utilizzo di un elemento dell'interfaccia TextKit nella progettazione dell'interfaccia utente dell'applicazione. Tastiera dettatura presenta inoltre il vantaggio di non richiedere tutte le richieste con privilegi speciali da app prima che possa essere utilizzato.

Le app che usano le nuove API riconoscimento vocale verranno richiedono autorizzazioni speciali per essere concesso dall'utente, poiché il riconoscimento vocale richiede la trasmissione e l'archiviazione temporanea dei dati nei server di Apple. Consultare il nostro [miglioramenti di Privacy e sicurezza](~/ios/app-fundamentals/security-privacy.md) documentazione per informazioni dettagliate.

Mentre la dettatura tastiera è facile da implementare, dotati diverse limitazioni e gli svantaggi:

- Richiede l'utilizzo di un campo di Input di testo e la visualizzazione di una tastiera.
- Viene eseguito con l'audio in tempo reale solo di input e l'applicazione non ha alcun controllo sul processo di registrazione audio.
- Non fornisce alcun controllo sulla lingua utilizzata per interpretare vocale dell'utente.
- Non è possibile per l'app per sapere se il pulsante dettatura è anche disponibile per l'utente.
- L'app non è possibile personalizzare il processo di registrazione audio.
- Fornisce un set molto superficiale di risultati che non dispone di informazioni quali intervalli di tempo e di confidenza.

### <a name="speech-recognition-api"></a>L'API di riconoscimento vocale

Nuovo in iOS 10, Apple ha rilasciato l'API di riconoscimento vocale che fornisce un modo più efficace per un'app iOS implementare il riconoscimento vocale. Questa API è lo stesso utilizzato per potenziare Siri sia dettatura tastiera Apple ed è in grado di fornire la trascrizione veloce con precisione all'avanguardia.

I risultati restituiti dall'API di riconoscimento vocale sono personalizzati in modo trasparente per singoli utenti, senza dover raccogliere o accedere ai dati utente privati app.

L'API di riconoscimento vocale fornisce risultati al chiamante app in quasi in tempo reale, come l'utente è generale e fornisce ulteriori informazioni sui risultati della conversione di solo testo. Sono inclusi:

- Si dice che più interpretazioni del quale l'utente.
- Livelli di confidenza per le singole traduzioni.
- Informazioni di temporizzazione.

Come descritto in precedenza, l'audio per la conversione può essere fornito sia da un feed in tempo reale, o da origine pre-registrato e in tutti i dialetti supportati da iOS 10 e oltre 50 lingue.

L'API di riconoscimento vocale può essere utilizzato in qualsiasi dispositivo iOS che eseguono iOS 10 e nella maggior parte dei casi, richiede una connessione internet attiva poiché la maggior parte delle traduzioni viene eseguita per i server Apple. Ciò premesso, alcuni iOS più recente dispositivi supportano always on, sul dispositivo traduzioni delle lingue specifiche.

Apple include un'API di disponibilità per determinare se una determinata lingua è disponibile per la conversione nel momento attuale. L'app deve usare questa API anziché eseguire direttamente il test per la connettività internet stesso.

Come indicato in precedenza nella sezione dettatura tastiera, il riconoscimento vocale richiede la trasmissione e l'archiviazione temporanea dei dati nel server Apple su internet e pertanto l'app _deve_ richiesta l'autorizzazione dell'utente per eseguire riconoscimento includendo il `NSSpeechRecognitionUsageDescription` chiave nel relativo `Info.plist` file e la chiamata di `SFSpeechRecognizer.RequestAuthorization` (metodo). 

In base all'origine dell'audio in uso per il riconoscimento vocale, altre modifiche all'app `Info.plist` file potrebbe essere necessario. Consultare il nostro [miglioramenti di Privacy e sicurezza](~/ios/app-fundamentals/security-privacy.md) documentazione per informazioni dettagliate.

## <a name="adopting-speech-recognition-in-an-app"></a>Adozione di riconoscimento vocale in un'App

Esistono quattro passaggi principali che lo sviluppatore deve intraprendere per adottare il riconoscimento vocale in un'app per iOS:

- Fornire una descrizione dell'utilizzo dell'app `Info.plist` file utilizzando il `NSSpeechRecognitionUsageDescription` chiave. Ad esempio, un'app della fotocamera potrebbe includere la seguente descrizione _"In questo modo è possibile sfruttare una foto semplicemente pronunciando la parola"cheese"."_
- Richiedere l'autorizzazione chiamando il `SFSpeechRecognizer.RequestAuthorization` metodo per presentare una spiegazione (fornito nel `NSSpeechRecognitionUsageDescription` chiave sopra) del motivo per cui l'app richiede vocale riconoscimento all'utente in una finestra di dialogo di accesso e consentire loro di accettare o rifiutare.
- Creare una richiesta di riconoscimento vocale:
    * Per l'audio preregistrato su disco, utilizzare la `SFSpeechURLRecognitionRequest` classe.
    * Per l'audio in tempo reale o audio dalla memoria, utilizzare la `SFSPeechAudioBufferRecognitionRequest` classe.
- Passare la richiesta di riconoscimento vocale per un riconoscimento vocale (`SFSpeechRecognizer`) per iniziare il riconoscimento. L'app può contenere facoltativamente su restituito `SFSpeechRecognitionTask` per monitorare e tenere traccia dei risultati di riconoscimento.

Questa procedura verrà descritta in dettaglio di seguito.

### <a name="providing-a-usage-description"></a>Fornire una descrizione dell'utilizzo.

Per fornire la `NSSpeechRecognitionUsageDescription` chiave nel `Info.plist` file, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su di `Info.plist` file per aprirlo e modificarlo.
2. Passare il **origine** Vista: 

    [![](speech-images/speech02.png "La visualizzazione origine")](speech-images/speech02.png#lightbox)
3. Fare clic su **Aggiungi nuova voce**, immettere `NSSpeechRecognitionUsageDescription` per il **proprietà**, `String` per il **tipo** e **descrizione dell'utilizzo** come il **valore**. Ad esempio: 

    [![](speech-images/speech03.png "Aggiunta di NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Se l'applicazione gestirà trascrizione audio in tempo reale, sarà necessaria anche una descrizione dell'utilizzo di microfono. Fare clic su **Aggiungi nuova voce**, immettere `NSMicrophoneUsageDescription` per il **proprietà**, `String` per il **tipo** e **descrizione dell'utilizzo** come il **valore**. Ad esempio: 

    [![](speech-images/speech04.png "Aggiunta di NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. Salvare le modifiche apportate al file.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su di `Info.plist` file per aprirlo e modificarlo.
3. Fare clic su **Aggiungi nuova voce**, immettere `NSSpeechRecognitionUsageDescription` per il **proprietà**, `String` per il **tipo** e **descrizione dell'utilizzo** come il **valore**. Ad esempio: 

    [![](speech-images/speech03w.png "Aggiunta di NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. Se l'applicazione gestirà trascrizione audio in tempo reale, sarà necessaria anche una descrizione dell'utilizzo di microfono. Fare clic su **Aggiungi nuova voce**, immettere `NSMicrophoneUsageDescription` per il **proprietà**, `String` per il **tipo** e **descrizione dell'utilizzo** come il **valore**. Ad esempio: 

    [![](speech-images/speech04w.png "Aggiunta di NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Salvare le modifiche apportate al file.

-----

> [!IMPORTANT]
> **Nota:** incapacità di fornire uno dei precedenti `Info.plist` chiavi (`NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription`) può comportare l'app non superati senza avviso quando si tenta di accedere il riconoscimento vocale o il microfono di audio in tempo reale.




### <a name="requesting-authorization"></a>La richiesta di autorizzazione

Per richiedere l'autorizzazione utente richiesto che consente all'app di accedere il riconoscimento vocale, modificare la classe di Controller di visualizzazione principale e aggiungere il codice seguente:

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

Il `RequestAuthorization` metodo il `SFSpeechRecognizer` classe richiederà autorizzazione da parte dell'utente per il riconoscimento vocale accesso utilizzando il motivo per cui lo sviluppatore di `NSSpeechRecognitionUsageDescription` la chiave del `Info.plist` file.

Oggetto `SFSpeechRecognizerAuthorizationStatus` risultato viene restituito per il `RequestAuthorization` routine di callback del metodo che può essere usata per intervenire in base alle autorizzazioni dell'utente. 

> [!IMPORTANT]
> **Nota:** Apple suggerisce in attesa fino a quando l'utente ha avviato un'azione nell'app che richiede il riconoscimento vocale prima di richiedere l'autorizzazione.

### <a name="recognizing-pre-recorded-speech"></a>Il riconoscimento vocale pre-registrato

Se l'applicazione è richiesto il riconoscimento vocale da un file WAV o MP3 pre-registrato, è possibile utilizzare il codice seguente:

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

Esaminando questo codice in dettaglio, in primo luogo, tenta di creare un riconoscimento vocale (`SFSpeechRecognizer`). Se la lingua predefinita non è supportata per il riconoscimento vocale, `null` viene restituito e la chiusura delle funzioni.

Se il riconoscimento vocale è disponibile per la lingua predefinita, l'app controlla per vedere se è attualmente disponibile per l'utilizzo di riconoscimento di `Available` proprietà. Riconoscimento, ad esempio, potrebbe non essere disponibile se il dispositivo non ha una connessione internet attiva.

Oggetto `SFSpeechUrlRecognitionRequest` viene creato dal `NSUrl` percorso del file pre-registrato del dispositivo iOS, viene passato per il riconoscimento vocale per l'elaborazione con una routine di callback.

Quando viene chiamato il callback, se il `NSError` non `null` si è verificato un errore che deve essere gestito. Poiché il riconoscimento vocale viene eseguito in modo incrementale, la routine di callback può essere chiamata più di una volta il `SFSpeechRecognitionResult.Final` proprietà viene testata per verificare se la conversione è stata completata e viene scritto versione migliore della traslazione (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Il riconoscimento vocale in tempo reale

Se l'applicazione è richiesto il riconoscimento vocale in tempo reale, il processo è molto simile al riconoscimento vocale pre-registrato. Ad esempio:

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

Esaminando questo codice in dettaglio, vengono creati per gestire il processo di riconoscimento più variabili private:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Usa Foundation AV per registrare l'audio che verrà passata a un `SFSpeechAudioBufferRecognitionRequest` per gestire la richiesta di riconoscimento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

L'applicazione tenta di avviare la registrazione e vengono gestiti gli errori se non è possibile avviare la registrazione:

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

Il callback viene utilizzato in modo simile a quella usata in precedenza il riconoscimento vocale pre-registrato.

Se la registrazione è stata interrotta dall'utente, il motore di Audio e la richiesta di riconoscimento vocale vengono informati:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Se l'utente annulla il riconoscimento, il motore di Audio e l'attività di riconoscimento vengono informati:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

È importante chiamare `RecognitionTask.Cancel` se l'utente annulla la traduzione per liberare memoria e processore del dispositivo.

> [!IMPORTANT]
> **Nota:** incapacità di fornire il `NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription` `Info.plist` chiavi possono comportare l'app non superati senza avviso quando si tenta di accedere il riconoscimento vocale o il microfono di audio in tempo reale (`var node = AudioEngine.InputNode;`). Vedere il **fornisce una descrizione dell'utilizzo** precedente sezione per ulteriori informazioni.

## <a name="speech-recognition-limits"></a>Limiti di riconoscimento vocale

Quando si lavora con riconoscimento vocale in un'app iOS, Apple impone le limitazioni seguenti:

- Il riconoscimento vocale è disponibile per tutte le app, ma il relativo utilizzo non è un numero illimitato:
    - I dispositivi iOS singoli sono un numero limitato di riconoscimenti che possono essere eseguite al giorno.
    - Le applicazioni verranno limitate a livello globale in base a una richiesta al giorno.
- L'applicazione deve essere preparata per la gestione connessione di rete di riconoscimento vocale e gli errori di limite di utilizzo della frequenza.
- Il riconoscimento vocale può avere un costo elevato della batteria sia il traffico di rete elevata sul dispositivo iOS dell'utente, per questo motivo, Apple impone un limite di durata audio strict di circa un minuto di riconoscimento vocale max.

Se un'app regolarmente sta superando i limiti di limitazione della velocità, Apple richiede che lo sviluppatore di contattarli.

## <a name="privacy-and-usability-considerations"></a>Usabilità considerazioni su privacy e

Apple ha il seguente suggerimento per essere trasparente e rispettare la privacy dell'utente quando si include il riconoscimento vocale in un'app per iOS:

- Durante la registrazione vocale dell'utente, assicurarsi di indicare chiaramente che la registrazione viene eseguita nell'interfaccia utente dell'applicazione. Ad esempio, l'app può riprodurre un suono un "registrazione" e un indicatore di registrazione.
- Non usare il riconoscimento vocale per le informazioni utente riservate, ad esempio password, i dati di integrità o informazioni finanziarie.
- Mostra i risultati di riconoscimento _prima_ che agisce su di essi. Questo non solo fornisce feedback sulla quali app sono in corso, ma consente all'utente di gestire gli errori di riconoscimento non appena vengono eseguite.

## <a name="summary"></a>Riepilogo

In questo articolo ha presentato la nuova API di riconoscimento vocale e la visualizzazione come implementarlo in un'app xamarin per supportare il riconoscimento vocale continua e trascrivere vocale (da flussi audio in tempo reale o registrati) nel testo. 



## <a name="related-links"></a>Collegamenti correlati

- [SpeakToMe (esempio)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
