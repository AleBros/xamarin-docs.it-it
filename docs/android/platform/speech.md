---
title: Sintesi vocale Android
description: Questo articolo illustra le nozioni di base sull'uso dello spazio dei nomi Android. Speech molto potente. Sin dall'inizio, Android è stato in grado di riconoscere il riconoscimento vocale ed emetterlo come testo. Si tratta di un processo relativamente semplice. Per sintesi vocale, tuttavia, il processo è più necessario, in quanto non solo è necessario tenere in considerazione il motore vocale, ma anche le lingue disponibili e installate dal sistema di sintesi vocale (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019759"
---
# <a name="android-speech"></a>Sintesi vocale Android

_Questo articolo illustra le nozioni di base sull'uso dello spazio dei nomi Android. Speech molto potente. Sin dall'inizio, Android è stato in grado di riconoscere il riconoscimento vocale ed emetterlo come testo. Si tratta di un processo relativamente semplice. Per sintesi vocale, tuttavia, il processo è più necessario, in quanto non solo è necessario tenere in considerazione il motore vocale, ma anche le lingue disponibili e installate dal sistema di sintesi vocale (TTS)._

## <a name="speech-overview"></a>Panoramica del riconoscimento vocale

Disporre di un sistema, che "comprende" riconoscimento vocale e enuncia di testo che viene digitato, sintesi vocale e Sintesi vocale, è un'area in continua crescita nello sviluppo per dispositivi mobili, in quanto la richiesta di comunicazione naturale con i dispositivi aumenta. Ci sono molte istanze in cui una funzionalità che converte il testo in sintesi vocale, o viceversa, è uno strumento molto utile da incorporare nell'applicazione Android.

Ad esempio, con il morsetto premuto sull'uso del telefono cellulare durante la guida, gli utenti vogliono un modo libero per gestire i propri dispositivi. I diversi fattori di forma Android, come Android Wear, e l'inclusione sempre più ampia di quelli che possono usare i dispositivi Android, ad esempio i tablet e i rilievi di note, hanno creato un grande interesse per le applicazioni TTS.

Google fornisce allo sviluppatore un ampio set di API nello spazio dei nomi Android. Speech per coprire la maggior parte delle istanze di creazione di un dispositivo "riconoscimento vocale", ad esempio il software progettato per la cecità.  Lo spazio dei nomi include la funzionalità per consentire la traduzione del testo in sintesi vocale tramite `Android.Speech.Tts`, il controllo sul motore usato per eseguire la traduzione, oltre a un numero di `RecognizerIntent`che consentono di convertire il riconoscimento vocale in testo.

Mentre le funzionalità sono disponibili per la comprensione del riconoscimento vocale, esistono limitazioni in base all'hardware usato. È improbabile che il dispositivo interpreti correttamente tutti gli elementi che vi parlano in ogni lingua disponibile.

## <a name="requirements"></a>Requisiti

Non sono previsti requisiti speciali per questa guida, ad eccezione del dispositivo con microfono e altoparlante.

Il nucleo di un riconoscimento vocale per l'interpretazione dei dispositivi Android è l'uso di un `Intent` con un `OnActivityResult`corrispondente.
È importante, tuttavia, riconoscere che la voce non è riconosciuta, ma interpretata nel testo. La differenza è importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>Differenza tra la comprensione e l'interpretazione

Una semplice definizione di comprensione è che si è in grado di determinare in base al tono e al contesto il vero significato di ciò che viene detto. Per interpretare solo le parole e restituirle in un altro formato.

Si consideri l'esempio seguente che viene usato nella conversazione giornaliera:

<kbd>Ciao come stai?</kbd>

Senza flessione (enfasi posta su parole o parti di parole specifiche), si tratta di una semplice domanda. Tuttavia, se alla riga viene applicata una velocità lenta, l'utente in ascolto rileverà che il richiedente non è troppo felice e potrebbe essere necessario tifare o che il richiedente è inadatto. Se l'enfasi viene posizionata su "are", l'utente che chiede è in genere più interessato alla risposta.

Senza un'elaborazione audio piuttosto potente per sfruttare la flessione e un grado di intelligenza artificiale per comprendere il contesto, il software non può neanche iniziare a capire cosa ne è stato detto, il migliore è un semplice telefono che consente di convertire il riconoscimento vocale in testo.

## <a name="setting-up"></a>Impostazione

Prima di usare il sistema di riconoscimento vocale, è sempre consigliabile verificare per verificare che il dispositivo disponga di un microfono. Si verificherà un piccolo tentativo di eseguire l'app in un pad Kindle o Google note senza un microfono installato.

Nell'esempio di codice riportato di seguito viene illustrata l'esecuzione di query se è disponibile un microfono e, in caso contrario, per creare un avviso. Se a questo punto non sono disponibili microfoni, è possibile uscire dall'attività o disabilitare la possibilità di registrare la voce.

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>Creazione della finalità

Lo scopo del sistema di riconoscimento vocale usa un particolare tipo di finalità denominato `RecognizerIntent`. Questa finalità controlla un numero elevato di parametri, tra cui il tempo di attesa con il silenzio fino a quando la registrazione non viene considerata, eventuali lingue aggiuntive da riconoscere e restituire e il testo da includere nella finestra di dialogo modale del `Intent`come mezzo di istruzione. In questo frammento di codice, `VOICE` è un `readonly int` utilizzato per il riconoscimento in `OnActivityResult`.

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>Conversione del riconoscimento vocale

Il testo interpretato dal riconoscimento vocale verrà recapitato all'interno dell'`Intent`, che viene restituito quando l'attività è stata completata ed è possibile accedervi tramite `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Verrà restituito un `IList<string>`, di cui l'indice può essere utilizzato e visualizzato, a seconda del numero di linguaggi richiesti nella finalità del chiamante (e specificati nell'`RecognizerIntent.ExtraMaxResults`). Come per qualsiasi elenco, tuttavia, è opportuno verificare che siano presenti dati da visualizzare.

Quando si è in attesa del valore restituito di una `StartActivityForResult`, è necessario fornire il metodo `OnActivityResult`.

Nell'esempio seguente `textBox` è un `TextBox` usato per l'output di ciò che è stato determinato. Potrebbe anche essere usato per passare il testo a una forma di interprete e da qui, l'applicazione può confrontare il testo e il ramo con un'altra parte dell'applicazione.

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>Sintesi vocale

Il testo in sintesi vocale non è completamente inverso del testo e si basa su due componenti chiave un motore di sintesi vocale installato nel dispositivo e un linguaggio da installare.

In gran parte, i dispositivi Android sono dotati del servizio predefinito Google TTS installato e di almeno una lingua. Questa operazione viene stabilita quando il dispositivo viene configurato per la prima volta e si basa sulla posizione in cui si trova il dispositivo. ad esempio, un telefono configurato in Germania installerà la lingua tedesca, mentre una in America avrà l'inglese americano.

### <a name="step-1---instantiating-texttospeech"></a>Passaggio 1: creazione di un'istanza di TextToSpeech

`TextToSpeech` possono essere necessari fino a 3 parametri, le prime due sono obbligatorie e la terza è facoltativa (`AppContext`, `IOnInitListener``engine`). Il listener viene usato per eseguire l'associazione al servizio e verificare la presenza di errori con il motore di un numero qualsiasi di motori di sintesi vocale disponibili. Come minimo, il dispositivo avrà il motore di Google.

### <a name="step-2---finding-the-languages-available"></a>Passaggio 2: ricerca delle lingue disponibili

La classe `Java.Util.Locale` contiene un metodo utile denominato `GetAvailableLocales()`. Questo elenco di lingue supportate dal motore di riconoscimento vocale può quindi essere testato con le lingue installate.

È molto semplice generare l'elenco dei linguaggi "compresi". Sarà sempre presente una lingua predefinita (la lingua impostata dall'utente al momento della prima impostazione del dispositivo), quindi in questo esempio il `List<string>` ha "default" come primo parametro, il resto dell'elenco verrà riempito a seconda del risultato della `textToSpeech.IsLanguageAvailable(locale)`.

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

Questo codice chiama [TextToSpeech. IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) per verificare se il pacchetto di lingua per le impostazioni locali specificate è già presente nel dispositivo.
Questo metodo restituisce un [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), che indica se la lingua delle impostazioni locali passate è disponibile. Se `LanguageAvailableResult` indica che la lingua è `NotSupported`, non è disponibile alcun pacchetto vocale (anche per il download) per tale lingua. Se `LanguageAvailableResult` è impostato su `MissingData`, è possibile scaricare un nuovo pacchetto di lingua come illustrato di seguito nel passaggio 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Passaggio 3: impostazione della velocità e del pitch

Android consente all'utente di modificare il suono del discorso modificando il `SpeechRate` e il `Pitch` (la velocità e il tono del discorso). Questa operazione va da 0 a 1, con un discorso "normale" pari a 1 per entrambi.

### <a name="step-4---testing-and-loading-new-languages"></a>Passaggio 4: test e caricamento di nuove lingue

Il download di una nuova lingua viene eseguito tramite un `Intent`. Il risultato di questa finalità causa la chiamata del metodo [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) . A differenza dell'esempio di riconoscimento vocale (che ha usato [RecognizerIntent](xref:Android.Speech.RecognizerIntent) come parametro di `PutExtra` per la `Intent`), i test e il caricamento di `Intent`s sono basati su `Action`:

- [TextToSpeech. Engine. ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; avvia un'attività dal motore di `TextToSpeech` della piattaforma per verificare la corretta installazione e la disponibilità delle risorse di lingua nel dispositivo.

- [TextToSpeech. Engine. ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; avvia un'attività che richiede all'utente di scaricare le lingue necessarie.

L'esempio di codice seguente illustra come usare queste azioni per testare le risorse di lingua e scaricare un nuovo linguaggio:

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` verifica la disponibilità delle risorse della lingua. `OnActivityResult` viene richiamato al completamento del test. Se è necessario scaricare le risorse della lingua, `OnActivityResult` attiva l'azione `TextToSpeech.Engine.ActionInstallTtsData` per avviare un'attività che consente all'utente di scaricare le lingue necessarie. Si noti che questa implementazione di `OnActivityResult` non controlla il codice `Result` perché, in questo esempio semplificato, la determinazione è già stata resa necessaria per il download del pacchetto di lingua.

L'azione `TextToSpeech.Engine.ActionInstallTtsData` fa in modo che l'attività **dati voce Google TTS** venga presentata all'utente per la scelta delle lingue da scaricare:

![Attività dati voce Google TTS](speech-images/01-google-tts-voice-data.png)

Ad esempio, l'utente potrebbe selezionare il francese e fare clic sull'icona di download per scaricare i dati vocali in francese:

![Esempio di download della lingua francese](speech-images/02-selecting-french.png)

L'installazione di questi dati avviene automaticamente al termine del download.

### <a name="step-5---the-ioninitlistener"></a>Passaggio 5: IOnInitListener

Affinché un'attività sia in grado di convertire il testo in sintesi vocale, è necessario implementare il metodo di interfaccia `OnInit` (si tratta del secondo parametro specificato per la creazione di un'istanza della classe `TextToSpeech`). Viene inizializzato il listener e viene verificato il risultato.

Il listener deve verificare sia `OperationResult.Success` che `OperationResult.Failure` almeno.
Nell'esempio seguente viene illustrato solo quanto segue:

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>Riepilogo

In questa guida sono state esaminate le nozioni di base per la conversione di testo in sintesi vocale e vocale e i possibili metodi di inclusione nelle app. Sebbene non coprano ogni particolare caso, è ora necessario avere una conoscenza di base del modo in cui viene interpretato il riconoscimento vocale, come installare nuove lingue e come aumentare il inclusione delle app.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Sintesi vocale (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Sintesi vocale (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Spazio dei nomi Android. Speech](xref:Android.Speech)
- [Spazio dei nomi Android. Speech. TTS](xref:Android.Speech.Tts)
