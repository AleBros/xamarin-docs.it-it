---
title: Riconoscimento vocale Android
description: Questo articolo illustra le nozioni di base sull'uso del potente spazio dei nomi Android.Speech.This article covers the basics of using the very powerful Android.Speech namespace. Fin dalla sua nascita, Android è stato in grado di riconoscere il parlato e l'output come testo. Si tratta di un processo relativamente semplice. Per la sintesi vocale, tuttavia, il processo è più complesso, in quanto non solo il motore di riconoscimento vocale deve essere preso in considerazione, ma anche le lingue disponibili e installate dal sistema TTS (Text To Speech).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019759"
---
# <a name="android-speech"></a>Riconoscimento vocale Android

_Questo articolo illustra le nozioni di base sull'uso del potente spazio dei nomi Android.Speech.This article covers the basics of using the very powerful Android.Speech namespace. Fin dalla sua nascita, Android è stato in grado di riconoscere il parlato e l'output come testo. Si tratta di un processo relativamente semplice. Per la sintesi vocale, tuttavia, il processo è più complesso, in quanto non solo il motore di riconoscimento vocale deve essere preso in considerazione, ma anche le lingue disponibili e installate dal sistema TTS (Text To Speech)._

## <a name="speech-overview"></a>Panoramica del riconoscimento vocale

Avere un sistema, che "capisce" il linguaggio umano ed enuncia ciò che viene digitato -Speech to Text, e Text to Speech - è un'area in continua crescita all'interno dello sviluppo mobile con l'aumentare della domanda di comunicazione naturale con i nostri dispositivi. Ci sono molti casi in cui avere una funzione che converte il testo in voce, o viceversa, è uno strumento molto utile da incorporare nell'applicazione Android.

Ad esempio, con il morsetto verso il basso sull'uso del telefono cellulare durante la guida, gli utenti vogliono un modo senza mani di utilizzare i loro dispositivi. La pletora di diversi fattori di forma Android, come Android Wear, e l'inclusione sempre più ampia di quelli in grado di utilizzare dispositivi Android (come tablet e note pad), ha creato una maggiore attenzione sulle grandi applicazioni di tintura.

Google fornisce allo sviluppatore un ricco set di API nello spazio dei nomi Android.Speech per coprire la maggior parte delle istanze di rendere un dispositivo "consapevole" (ad esempio software progettato per i non vedenti).  Lo spazio dei nomi include la funzionalità `Android.Speech.Tts`per consentire la traduzione del testo in voce, `RecognizerIntent`il controllo sul motore utilizzato per eseguire la traduzione, nonché un numero di s che consentono la conversione del parlato in testo.

Mentre le strutture sono lì per la parola da capire, ci sono limitazioni basate sull'hardware utilizzato. E 'improbabile che il dispositivo interpreterà con successo tutto ciò che gli è stato parlato in ogni lingua disponibile.

## <a name="requirements"></a>Requisiti

Non ci sono requisiti speciali per questa guida, a parte il dispositivo che ha un microfono e un altoparlante.

Il nucleo di un dispositivo Android che `Intent` interpreta il `OnActivityResult`parlato è l'uso di un con un corrispondente .
È importante, tuttavia, riconoscere che il discorso non viene compreso, ma interpretato in testo. La differenza è importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La differenza tra comprensione e interpretazione

Una semplice definizione di comprensione è che si è in grado di determinare per tono e contesto il vero significato di ciò che viene detto. Interpretare significa solo prendere le parole e metterle in un'altra forma.

Si consideri il seguente semplice esempio che viene utilizzato nella conversazione quotidiana:

<kbd>Ciao come stai?</kbd>

Senza inflessione (enfasi posta su parole specifiche o parti di parole), è una domanda semplice. Tuttavia, se un ritmo lento viene applicato alla linea, la persona che ascolta rileverà che l'asker non è troppo felice e forse ha bisogno di rallegrarsi o che l'asker non sta bene. Se l'enfasi è posta su "sono", la persona che chiede è di solito più interessata alla risposta.

Senza un'elaborazione audio abbastanza potente per fare uso dell'inflessione, e un grado di intelligenza artificiale (AI) per capire il contesto, il software non può nemmeno iniziare a capire ciò che è stato detto, il meglio che un semplice telefono può fare è convertire il discorso in testo.

## <a name="setting-up"></a>Configurazione

Prima di utilizzare il sistema vocale, è sempre consigliabile verificare che il dispositivo disponga di un microfono. Non avrebbe senso provare a eseguire la tua app su un blocco note Kindle o Google senza un microfono installato.

Nell'esempio di codice riportato di seguito viene illustrata l'esecuzione di query se è disponibile un microfono e, in caso contrario, per creare un avviso. Se non è disponibile alcun microfono a questo punto si dovrebbe uscire l'attività o disabilitare la possibilità di registrare il discorso.

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

### <a name="creating-the-intent"></a>Creazione dell'intento

L'intento per il sistema di riconoscimento vocale utilizza un particolare tipo di finalità denominato `RecognizerIntent`. Questa finalità controlla un gran numero di parametri, tra cui il tempo di attesa con silenzio fino a `Intent`quando la registrazione viene considerata finita, eventuali lingue aggiuntive da riconoscere e l'output e qualsiasi testo da includere nella finestra di dialogo modale come mezzo di istruzione. In questo `VOICE` frammento, è `readonly int` `OnActivityResult`un utilizzato per il riconoscimento in .

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

### <a name="conversion-of-the-speech"></a>Conversione del discorso

Il testo interpretato dal discorso `Intent`verrà recapitato all'interno di , che `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`viene restituito al termine dell'attività e a cui si accede tramite . Verrà restituito `IList<string>`un oggetto , di cui l'indice può essere utilizzato e visualizzato, a seconda `RecognizerIntent.ExtraMaxResults`del numero di lingue richieste nell'intento del chiamante (e specificato in ). Come con qualsiasi elenco, però, vale la pena controllare per assicurarsi che ci sono dati da visualizzare.

Quando si è in `StartActivityForResult`ascolto `OnActivityResult` per il valore restituito di un oggetto , è necessario fornire il metodo .

Nell'esempio seguente, `textBox` `TextBox` è un usato per l'output di ciò che è stato dettato. Potrebbe anche essere usato per passare il testo a qualche forma di interprete e da lì, l'applicazione può confrontare il testo e ramo per un'altra parte dell'applicazione.

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

La sintesi vocale non è il contrario del discorso al testo e si basa su due componenti chiave; un motore di sintesi vocale installato sul dispositivo e una lingua da installare.

In gran parte, i dispositivi Android sono dotati del servizio di ttS Google predefinito installato e almeno una lingua. Questo viene stabilito quando il dispositivo è configurato per la prima volta e sarà basato su dove si trova il dispositivo al momento (ad esempio, un telefono configurato in Germania installerà la lingua tedesca, mentre uno in America avrà inglese americano).

### <a name="step-1---instantiating-texttospeech"></a>Passaggio 1 - Creazione di un'istanza di TextToSpeechStep 1 - Instantiating TextToSpeech

`TextToSpeech`può richiedere fino a 3 parametri, i primi due`AppContext` `IOnInitListener`sono `engine`necessari, il terzo è facoltativo ( , , ). Il listener viene usato per eseguire l'associazione al servizio e verificare la situazione di errore con il motore che è un numero qualsiasi di motori di sintesi vocale Android disponibili. Come minimo, il dispositivo avrà il motore di Google.

### <a name="step-2---finding-the-languages-available"></a>Fase 2 - Trovare le lingue disponibili

La `Java.Util.Locale` classe contiene un `GetAvailableLocales()`metodo utile denominato . Questo elenco di lingue supportate dal motore vocale può quindi essere testato rispetto alle lingue installate.

È una questione banale generare l'elenco delle lingue "comprese". Ci sarà sempre una lingua predefinita (la lingua che l'utente ha impostato `List<string>` quando ha impostato il dispositivo per la prima volta), quindi in `textToSpeech.IsLanguageAvailable(locale)`questo esempio ha "Default" come primo parametro, il resto dell'elenco verrà riempito a seconda del risultato del .

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

Questo codice chiama [TextToSpeech.IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) per verificare se il pacchetto della lingua per determinate impostazioni locali è già presente nel dispositivo.
Questo metodo restituisce un [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), che indica se la lingua per le impostazioni locali passate è disponibile. Se `LanguageAvailableResult` indica che la `NotSupported`lingua è , non è disponibile alcun pacchetto vocale (anche per il download) per tale lingua. Se `LanguageAvailableResult` è `MissingData`impostato su , è possibile scaricare un nuovo pacchetto linguistico come spiegato di seguito nel passaggio 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Fase 3 - Impostazione della velocità e del passo

Android consente all'utente di modificare il suono `SpeechRate` `Pitch` del discorso alterando il e (la velocità e il tono del discorso). Questo va da 0 a 1, con il discorso "normale" 1 per entrambi.

### <a name="step-4---testing-and-loading-new-languages"></a>Passaggio 4 - Test e caricamento di nuove lingueStep 4 - Testing and loading new languages

Il download di una nuova `Intent`lingua viene eseguito utilizzando un file . Il risultato di questa finalità fa sì che il [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) metodo da richiamare. A differenza dell'esempio di sintesi vocale (che `PutExtra` [utilizzava RecognizerIntent](xref:Android.Speech.RecognizerIntent) come `Action`parametro per il ), il testing e il `Intent`caricamento `Intent`s sono basati su:

- [TextToSpeech.Engine.ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; Avvia un'attività `TextToSpeech` dal motore della piattaforma per verificare la corretta installazione e disponibilità delle risorse di lingua nel dispositivo.

- [TextToSpeech.Engine.ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; Avvia un'attività che richiede all'utente di scaricare le lingue necessarie.

Nell'esempio di codice seguente viene illustrato come utilizzare queste azioni per testare le risorse della lingua e scaricare una nuova lingua:The following code example illustrates how to use these actions to test for language resources and download a new language:

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

`TextToSpeech.Engine.ActionCheckTtsData`per la disponibilità delle risorse linguistiche. `OnActivityResult`viene richiamato al completamento di questo test. Se è necessario scaricare `OnActivityResult` le risorse `TextToSpeech.Engine.ActionInstallTtsData` di lingua, attiva l'azione per avviare un'attività che consente all'utente di scaricare le lingue necessarie. Si noti che `OnActivityResult` questa `Result` implementazione non controlla il codice perché, in questo esempio semplificato, è già stata effettuata la determinazione che il pacchetto del linguaggio deve essere scaricato.

L'azione `TextToSpeech.Engine.ActionInstallTtsData` fa sì che l'attività dei dati vocali di **Google TTS** venga presentata all'utente per la scelta delle lingue da scaricare:

![Attività sui dati vocali di Google TTS](speech-images/01-google-tts-voice-data.png)

Ad esempio, l'utente potrebbe scegliere francese e fare clic sull'icona di download per scaricare i dati vocali francesi:

![Esempio di download della lingua francese](speech-images/02-selecting-french.png)

L'installazione di questi dati avviene automaticamente al termine del download.

### <a name="step-5---the-ioninitlistener"></a>Passaggio 5 - IOnInitListenerStep 5 - The IOnInitListener

Affinché un'attività sia in grado di convertire `OnInit` il testo in sintesi vocale, è necessario implementare il metodo di interfaccia (questo è il secondo parametro specificato per la creazione di istanze della `TextToSpeech` classe). In questo modo viene inizializzato il listener e viene verificato il risultato.

Il listener deve `OperationResult.Success` eseguire `OperationResult.Failure` il test per entrambi e almeno.
L'esempio seguente mostra solo questo:

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

In questa guida abbiamo esaminato le nozioni di base per la conversione di testo in voce e sintesi vocale e possibili metodi di come includerli all'interno delle proprie app. Anche se non coprono tutti i casi particolari, ora dovresti avere una comprensione di base di come viene interpretato il parlato, come installare nuove lingue e come aumentare l'inclusione delle tue app.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Sintesi vocale (esempio)Text to Speech (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Sintesi vocale (esempio)Speech to Text (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Spazio dei nomi Android.Speech](xref:Android.Speech)
- [Spazio dei nomi Android.Speech.Tts](xref:Android.Speech.Tts)
