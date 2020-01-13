---
title: Riconoscimento vocale in Xamarin.iOS
description: Questo articolo presenta il nuovo Speech API e Mostra come implementarlo in un'app Xamarin.iOS per supportare il riconoscimento vocale continuo e la traduzione vocale (da flussi audio in tempo reale o registrato) in testo.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 015be44f60dbf8cd2d70badd7c9edaf4e5a1d2a4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031462"
---
# <a name="speech-recognition-in-xamarinios"></a>Riconoscimento vocale in Xamarin.iOS

_Questo articolo presenta il nuovo Speech API e Mostra come implementarlo in un'app Xamarin.iOS per supportare il riconoscimento vocale continuo e la traduzione vocale (da flussi audio in tempo reale o registrato) in testo._

Una novità di iOS 10, Apple ha rilasciato l'API riconoscimento vocale, che consente a un'app iOS di supportare il riconoscimento vocale continuo e di trascrivere il discorso (da flussi audio in tempo reale o registrato) in testo.

Secondo Apple, l'API riconoscimento vocale offre le funzionalità e i vantaggi seguenti:

- Accuratezza elevata
- Stato dell'arte
- Facile da usare
- Fast
- Supporta più lingue
- Rispetta la privacy degli utenti

## <a name="how-speech-recognition-works"></a>Funzionamento del riconoscimento vocale

Il riconoscimento vocale viene implementato in un'app per iOS acquisendo audio in tempo reale o preregistrato (in una qualsiasi delle lingue vocali supportate dall'API) e passandolo a un riconoscimento vocale che restituisce una trascrizione in testo normale delle parole vocali.

[![](speech-images/speech01.png "How Speech Recognition Works")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dettatura tastiera

Quando la maggior parte degli utenti pensa al riconoscimento vocale in un dispositivo iOS, pensa all'Assistente vocale Siri incorporato, che è stato rilasciato insieme alla dettatura della tastiera in iOS 5 con iPhone 4S.

La dettatura della tastiera è supportata da qualsiasi elemento dell'interfaccia che supporta TextKit, ad esempio `UITextField` o `UITextArea`, e viene attivato dall'utente facendo clic sul pulsante di dettatura (direttamente a sinistra della barra spaziatrice) nella tastiera virtuale iOS.

Apple ha rilasciato le seguenti statistiche sulla dettatura della tastiera (raccolte da 2011):

- La dettatura della tastiera è stata ampiamente usata perché è stata rilasciata in iOS 5.
- Circa 65.000 app vengono usate al giorno.
- Circa un terzo di tutta la dettatura di iOS viene eseguita in un'app di terze parti.

La dettatura della tastiera è estremamente semplice da usare perché non richiede alcun intervento da parte dello sviluppatore, oltre all'uso di un elemento dell'interfaccia TextKit nella progettazione dell'interfaccia utente dell'app. La dettatura della tastiera ha anche il vantaggio di non richiedere richieste speciali di privilegi dall'app prima di poterla usare.

Per le app che usano le nuove API di riconoscimento vocale sono necessarie autorizzazioni speciali da concedere all'utente, poiché il riconoscimento vocale richiede la trasmissione e l'archiviazione temporanea dei dati nei server Apple. Per informazioni dettagliate, vedere la documentazione relativa ai miglioramenti per la [sicurezza e la privacy](~/ios/app-fundamentals/security-privacy.md) .

Sebbene la dettatura della tastiera sia facile da implementare, presenta diverse limitazioni e svantaggi:

- Richiede l'uso di un campo di input di testo e la visualizzazione di una tastiera.
- Funziona solo con input audio Live e l'app non ha alcun controllo sul processo di registrazione audio.
- Non fornisce alcun controllo sul linguaggio utilizzato per interpretare la voce dell'utente.
- Non esiste alcun modo per l'app per verificare se il pulsante di dettatura è ancora disponibile per l'utente.
- L'app non è in grado di personalizzare il processo di registrazione audio.
- Fornisce un set di risultati molto superficiale in cui mancano informazioni come la tempistica e la confidenza.

### <a name="speech-recognition-api"></a>API riconoscimento vocale

Una novità di iOS 10, Apple ha rilasciato l'API riconoscimento vocale, che consente a un'app iOS di implementare il riconoscimento vocale in modo più efficace. Questa API è la stessa usata da Apple per sfruttare Siri e la dettatura della tastiera ed è in grado di fornire una trascrizione veloce con lo stato dell'accuratezza dell'arte.

I risultati forniti dall'API riconoscimento vocale vengono personalizzati in modo trasparente per i singoli utenti, senza che l'app debba raccogliere o accedere a dati utente privati.

L'API riconoscimento vocale fornisce risultati all'app chiamante quasi in tempo reale mentre l'utente sta parlando e fornisce altre informazioni sui risultati della traduzione rispetto al solo testo. tra cui:

- Più interpretazioni di ciò che l'utente ha detto.
- Livelli di confidenza per le singole traduzioni.
- Informazioni sull'intervallo.

Come indicato in precedenza, l'audio per la traduzione può essere fornito da un feed live o da un'origine pre-registrata e in una delle oltre 50 lingue e i dialetti supportati da iOS 10.

L'API riconoscimento vocale può essere usata in qualsiasi dispositivo iOS che esegue iOS 10 e, nella maggior parte dei casi, richiede una connessione Internet in tempo reale, perché la maggior parte delle traduzioni viene eseguita nei server Apple. Ciò premesso, alcuni dispositivi iOS più recenti supportano la conversione su dispositivo always on, in una lingua specifica.

Apple ha incluso un'API di disponibilità per determinare se una lingua specificata è disponibile per la traduzione al momento corrente. L'app deve usare questa API invece di testare direttamente la connettività Internet.

Come indicato in precedenza nella sezione relativa alla dettatura della tastiera, il riconoscimento vocale richiede la trasmissione e l'archiviazione temporanea dei dati nei server Apple tramite Internet e, di conseguenza, l'app _deve_ richiedere l'autorizzazione dell'utente per eseguire il riconoscimento includendo chiave `NSSpeechRecognitionUsageDescription` nel file di `Info.plist` e chiamata al metodo `SFSpeechRecognizer.RequestAuthorization`. 

In base all'origine dell'audio usato per il riconoscimento vocale, potrebbe essere necessario apportare altre modifiche al file di `Info.plist` dell'app. Per informazioni dettagliate, vedere la documentazione relativa ai miglioramenti per la [sicurezza e la privacy](~/ios/app-fundamentals/security-privacy.md) .

## <a name="adopting-speech-recognition-in-an-app"></a>Adozione del riconoscimento vocale in un'app

Ci sono quattro passaggi principali che lo sviluppatore deve adottare per adottare il riconoscimento vocale in un'app iOS:

- Specificare una descrizione dell'utilizzo nel file di `Info.plist` dell'app usando la chiave di `NSSpeechRecognitionUsageDescription`. Ad esempio, un'app della fotocamera potrebbe includere la descrizione seguente: _"questa operazione consente di scattare una foto semplicemente pronunciando la parola" Cheese "._
- Richiedere l'autorizzazione chiamando il metodo `SFSpeechRecognizer.RequestAuthorization` per presentare una spiegazione (fornita nella chiave `NSSpeechRecognitionUsageDescription` precedente) del motivo per cui l'app vuole che il riconoscimento vocale acceda all'utente in una finestra di dialogo e ne consenta l'accettazione o il rifiuto.
- Creare una richiesta di riconoscimento vocale:
  - Per l'audio pre-registrato sul disco, usare la classe `SFSpeechURLRecognitionRequest`.
  - Per l'audio attivo (o audio dalla memoria), usare la classe `SFSPeechAudioBufferRecognitionRequest`.
- Passare la richiesta di riconoscimento vocale a un riconoscimento vocale (`SFSpeechRecognizer`) per avviare il riconoscimento. L'app può facoltativamente tenere il `SFSpeechRecognitionTask` restituito per monitorare e tenere traccia dei risultati del riconoscimento.

Questi passaggi verranno descritti in dettaglio di seguito.

### <a name="providing-a-usage-description"></a>Specifica di una descrizione dell'utilizzo

Per fornire la chiave di `NSSpeechRecognitionUsageDescription` necessaria nel file di `Info.plist`, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul file `Info.plist` per aprirlo per la modifica.
2. Passare alla visualizzazione **origine** : 

    [![](speech-images/speech02.png "The Source view")](speech-images/speech02.png#lightbox)
3. Fare clic su **Aggiungi nuova voce**, immettere `NSSpeechRecognitionUsageDescription` per la **Proprietà**`String` per il **tipo** e una **Descrizione utilizzo** come **valore**. Esempio: 

    [![](speech-images/speech03.png "Adding NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Se l'app gestisce la trascrizione audio in tempo reale, sarà necessaria anche una descrizione dell'utilizzo del microfono. Fare clic su **Aggiungi nuova voce**, immettere `NSMicrophoneUsageDescription` per la **Proprietà**`String` per il **tipo** e una **Descrizione utilizzo** come **valore**. Esempio: 

    [![](speech-images/speech04.png "Adding NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
5. Salvare le modifiche apportate al file.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul file `Info.plist` per aprirlo per la modifica.
2. Fare clic su **Aggiungi nuova voce**, immettere `NSSpeechRecognitionUsageDescription` per la **Proprietà**`String` per il **tipo** e una **Descrizione utilizzo** come **valore**. Esempio: 

    [![](speech-images/speech03w.png "Adding NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
3. Se l'app gestisce la trascrizione audio in tempo reale, sarà necessaria anche una descrizione dell'utilizzo del microfono. Fare clic su **Aggiungi nuova voce**, immettere `NSMicrophoneUsageDescription` per la **Proprietà**`String` per il **tipo** e una **Descrizione utilizzo** come **valore**. Esempio: 

    [![](speech-images/speech04w.png "Adding NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Salvare le modifiche apportate al file.

-----

> [!IMPORTANT]
> Se non si specificano le chiavi di `Info.plist` precedenti (`NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription`), l'app potrebbe non riuscire senza preavviso quando si tenta di accedere al riconoscimento vocale o al microfono per l'audio Live.

### <a name="requesting-authorization"></a>Richiesta dell'autorizzazione

Per richiedere l'autorizzazione utente necessaria che consente all'app di accedere al riconoscimento vocale, modificare la classe del controller di visualizzazione principale e aggiungere il codice seguente:

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

Il metodo `RequestAuthorization` della classe `SFSpeechRecognizer` richiede l'autorizzazione da parte dell'utente per accedere al riconoscimento vocale usando il motivo fornito dallo sviluppatore nella chiave di `NSSpeechRecognitionUsageDescription` del file `Info.plist`.

Viene restituito un risultato `SFSpeechRecognizerAuthorizationStatus` alla routine di callback del metodo `RequestAuthorization` che può essere utilizzata per eseguire un'azione in base all'autorizzazione dell'utente. 

> [!IMPORTANT]
> Apple suggerisce di attendere fino a quando l'utente non ha avviato un'azione nell'app che richiede il riconoscimento vocale prima di richiedere questa autorizzazione.

### <a name="recognizing-pre-recorded-speech"></a>Riconoscimento del riconoscimento vocale pre-registrato

Se l'app vuole riconoscere il riconoscimento vocale da un file WAV o MP3 pre-registrato, può usare il codice seguente:

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

Esaminando il codice in dettaglio, innanzitutto, tenta di creare un riconoscimento vocale (`SFSpeechRecognizer`). Se la lingua predefinita non è supportata per il riconoscimento vocale, viene restituito `null` e le funzioni vengono terminate.

Se il riconoscimento vocale è disponibile per la lingua predefinita, l'app verifica se è attualmente disponibile per il riconoscimento usando la proprietà `Available`. Ad esempio, il riconoscimento potrebbe non essere disponibile se il dispositivo non dispone di una connessione Internet attiva.

Viene creata una `SFSpeechUrlRecognitionRequest` dal `NSUrl` percorso del file pre-registrato nel dispositivo iOS e viene passato al riconoscimento vocale per l'elaborazione con una routine di callback.

Quando viene chiamato il callback, se il `NSError` non è `null` si è verificato un errore che deve essere gestito. Poiché il riconoscimento vocale viene eseguito in modo incrementale, la routine di callback può essere chiamata più volte, quindi la proprietà `SFSpeechRecognitionResult.Final` viene testata per verificare se la traduzione è completa e la versione migliore della traduzione viene scritta (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Riconoscimento della sintesi vocale in tempo reale

Se l'app vuole riconoscere la voce in tempo reale, il processo è molto simile a quello del riconoscimento vocale pre-registrato. Esempio:

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

Esaminando in dettaglio questo codice, vengono create diverse variabili private per gestire il processo di riconoscimento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

USA AV Foundation per registrare l'audio che verrà passato a un `SFSpeechAudioBufferRecognitionRequest` per gestire la richiesta di riconoscimento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

L'app tenta di avviare la registrazione e gli eventuali errori vengono gestiti se non è possibile avviare la registrazione:

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

Il callback viene usato in modo analogo a quello usato sopra per il riconoscimento vocale pre-registrato.

Se la registrazione viene arrestata dall'utente, vengono informati sia il motore audio che la richiesta di riconoscimento vocale:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Se l'utente annulla il riconoscimento, viene informato il motore audio e l'attività di riconoscimento:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

È importante chiamare `RecognitionTask.Cancel` se l'utente annulla la traduzione per liberare la memoria e il processore del dispositivo.

> [!IMPORTANT]
> Se non si specifica il `NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription` `Info.plist` chiavi, l'app potrebbe non riuscire senza preavviso quando si tenta di accedere al riconoscimento vocale o al microfono per l'audio Live (`var node = AudioEngine.InputNode;`). Per ulteriori informazioni, vedere la sezione **fornire una descrizione dell'utilizzo** .

## <a name="speech-recognition-limits"></a>Limiti di riconoscimento vocale

Quando si lavora con il riconoscimento vocale in un'app iOS, Apple impone le seguenti limitazioni:

- Il riconoscimento vocale è gratuito per tutte le app, ma il suo utilizzo non è illimitato:
  - I singoli dispositivi iOS hanno un numero limitato di riconoscimenti che possono essere eseguiti ogni giorno.
  - Le app verranno limitate a livello globale in base a una richiesta al giorno.
- L'app deve essere preparata a gestire gli errori di connessione di rete di riconoscimento vocale e limite di frequenza di utilizzo.
- Il riconoscimento vocale può avere un costo elevato sia per lo svuotamento della batteria sia per il traffico di rete elevato sul dispositivo iOS dell'utente. per questo motivo, Apple impone un limite di durata audio di circa un minuto di max.

Se un'app è soggetta regolarmente ai limiti di limitazione della frequenza, Apple chiede che lo sviluppatore li contatti.

## <a name="privacy-and-usability-considerations"></a>Considerazioni sulla privacy e sull'usabilità

Apple presenta il suggerimento seguente per essere trasparente e rispettando la privacy dell'utente quando si include il riconoscimento vocale in un'app iOS:

- Quando si registra la voce dell'utente, assicurarsi di indicare chiaramente che la registrazione viene applicata nell'interfaccia utente dell'app. Ad esempio, l'app potrebbe riprodurre un suono di "registrazione" e visualizzare un indicatore di registrazione.
- Non usare il riconoscimento vocale per informazioni riservate sull'utente, ad esempio password, dati di integrità o informazioni finanziarie.
- Mostra i risultati del riconoscimento _prima_ di agire su di essi. Non solo fornisce commenti e suggerimenti sulle operazioni eseguite dall'app, ma consente all'utente di gestire gli errori di riconoscimento Man mano che vengono apportati.

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato il nuovo Speech API e ha illustrato come implementarlo in un'app Xamarin.iOS per supportare il riconoscimento vocale continuo e la trascrizione vocale (da flussi audio in tempo reale o registrato) in testo. 

## <a name="related-links"></a>Collegamenti correlati

- [SpeakToMe (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-speaktome)
