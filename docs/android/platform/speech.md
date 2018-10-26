---
title: Riconoscimento vocale di Android
description: Questo articolo illustra le nozioni di base dell'utilizzo dello spazio dei nomi Android. Speech molto potente. Fin dalle sue origini, Android è stata in grado di riconoscimento vocale e restituirlo come testo. È un processo relativamente semplice. Per sintesi vocale, tuttavia, il processo è più complesso, poiché non solo il motore di riconoscimento vocale necessario tener conto, ma anche le lingue disponibili e installate dal sistema di sintesi vocale (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: e88f6e24cbf4c8b2f0c0486c6408e234e87066cc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104349"
---
# <a name="android-speech"></a>Riconoscimento vocale di Android

_Questo articolo illustra le nozioni di base dell'utilizzo dello spazio dei nomi Android. Speech molto potente. Fin dalle sue origini, Android è stata in grado di riconoscimento vocale e restituirlo come testo. È un processo relativamente semplice. Per sintesi vocale, tuttavia, il processo è più complesso, poiché non solo il motore di riconoscimento vocale necessario tener conto, ma anche le lingue disponibili e installate dal sistema di sintesi vocale (TTS)._

## <a name="speech-overview"></a>Panoramica di riconoscimento vocale

Disporre di un sistema, quali "riconosce" voce umana ed enunciates qual è la digitazione, riconoscimento vocale in testo e sintesi vocale, ovvero è un'area all'interno di sviluppo per dispositivi mobili in continua crescita di picco della domanda per la comunicazione naturale con i dispositivi. Sono presenti molte istanze in presenza di una funzionalità che converte il testo in sintesi vocale, o viceversa, è uno strumento molto utile per incorporare nell'applicazione android.

Ad esempio, con clamp verso il basso utilizzo telefono cellulare mentre si Guida, gli utenti desiderano un modo gratuito mani operativo i propri dispositivi. La vasta gamma di fattori di forma diversi Android, ad esempio Android Wear, e l'inclusione sempre più ampio di quelle in grado di usare i dispositivi Android (ad esempio Tablet e i riquadri nota), ha creato una maggiore attenzione su grandi applicazioni di sintesi vocale.

Google fornisce allo sviluppatore di una vasta gamma di API nello spazio dei nomi Android. Speech per coprire la maggior parte delle istanze di rendere i dispositivi "vocale compatibile con" (ad esempio, software progettato for the blind).  Lo spazio dei nomi include la possibilità di consentire che il testo da tradurre in riconoscimento vocale attraverso `Android.Speech.Tts`, controllare il motore usato per eseguire la traduzione, nonché un numero di `RecognizerIntent`s che consentono di riconoscimento vocale da convertire in testo.

Mentre le funzionalità sono presenti per il riconoscimento vocale essere riconosciuta, sono previste limitazioni in base all'hardware usato. È improbabile che il dispositivo interpreterà correttamente tutto ciò che si parla in tutte le lingue disponibili.

## <a name="requirements"></a>Requisiti

Non sono previsti requisiti speciali per questa Guida, diverso dal dispositivo che presenta un microfono e degli altoparlanti.

Alla base di un dispositivo Android l'interpretazione di riconoscimento vocale è l'uso di un' `Intent` con un corrispondente `OnActivityResult`.
È importante, tuttavia, per riconoscere che il riconoscimento vocale non è stato riconosciuto, ma interpretato al testo. La differenza è importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La differenza tra la comprensione e l'interpretazione

Una definizione di semplice comprensione è che si è in grado di determinare il vero significato di parlato tono e contesto. Per interpretare appena significa che sfruttare le parole e restituirli in un formato diverso.

Si consideri l'esempio seguente che viene utilizzata nella conversazione ogni giorno: 

<kbd>Ciao come stai?</kbd>

Senza flesso (attenzione a parole specifiche o parti di parole), è una semplice domanda. Tuttavia, se un ritmo lento viene applicato alla riga, la persona in ascolto rileverà che il richiedente non è molto soddisfatto e forse deve Grandissimo o che il richiedente è unwell. Se l'attenzione viene inserito nella "sono", la persona che richiede viene in genere più interessati a nella risposta.

Senza audio piuttosto potente elaborazione per rendere utilizzare l'inflessione e un livello di intelligenza artificiale (AI) per comprendere il contesto, il software non è possibile anche solo iniziare a comprendere ciò che è stato detto: il meglio possibile eseguire un semplice telefono è convertire il riconoscimento vocale in testo.

## <a name="setting-up"></a>Impostazione

Prima di usare il sistema di riconoscimento vocale, è sempre consigliabile controllare per verificare che il dispositivo dispone di un microfono. Non vi sarà poco senso provando a eseguire l'app in un blocco note Kindle o Google non dispongono di microfono installato.

Esempio di codice seguente illustra l'esecuzione di query se il microfono sia disponibile e in caso contrario, per creare un avviso. Se è disponibile alcun microfono a questo punto si potrebbe uscire dall'attività o disabilitare la possibilità di registrare il riconoscimento vocale.

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

### <a name="creating-the-intent"></a>Lo scopo di creazione

Lo scopo per il sistema di riconoscimento vocale utilizza un tipo particolare di finalità chiamato il `RecognizerIntent`. Questa finalità controlla un numero elevato di parametri, incluso il tempo di attesa con silenzio finché la registrazione è considerata rispetto, tutte le lingue aggiuntive per riconoscere e di output e qualsiasi testo da includere nel `Intent`della finestra di dialogo modale come mezzo di istruzione. In questo frammento di codice `VOICE` è un `readonly int` utilizzato per il riconoscimento in `OnActivityResult`.

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

Il testo interpretato del contenuto vocale verrà recapitato all'interno di `Intent`, che viene restituito quando l'attività è stata completata e è accessibili tramite `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Verrà restituito un `IList<string>`, di cui l'indice può essere utilizzato e visualizzato, a seconda del numero di lingue richieste nella finalità del chiamante (specificato nel `RecognizerIntent.ExtraMaxResults`). Come con qualsiasi elenco, tuttavia, vale la pena un controllo per assicurarsi che vi sia i dati da visualizzare.

Quando è in ascolto per il valore restituito di una `StartActivityForResult`, il `OnActivityResult` metodo deve essere fornito.

Nell'esempio riportato di seguito `textBox` è un `TextBox` usato per l'output di ciò che è stato determinato. Può ugualmente essere usata per passare il testo in una forma dell'interprete e da lì, l'applicazione in grado di confrontare il testo e il ramo a un'altra parte dell'applicazione.

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

Sintesi vocale non è abbastanza l'opposto di riconoscimento vocale in testo e si basa su due componenti principali. viene installato un motore di sintesi vocale sul dispositivo e una lingua da installare.

In gran parte, i dispositivi Android forniti con il valore predefinito installato il servizio Google TTS e almeno una lingua. Ciò viene stabilito al momento il dispositivo prima di tutto configurare e si baserà in cui il dispositivo è al momento (ad esempio, un telefono configurato in Germania installerà la lingua tedesca, mentre uno in America avrà l'inglese americano).

### <a name="step-1---instantiating-texttospeech"></a>Passaggio 1: creazione di un'istanza TextToSpeech

`TextToSpeech` può richiedere fino a 3 parametri, le prime due sono necessarie con la terza sia facoltativo (`AppContext`, `IOnInitListener`, `engine`). Il listener viene utilizzato per eseguire l'associazione al servizio e a prova di errore con il motore in fase di un numero qualsiasi di motori di disponibile Android sintesi vocale. Come minimo, il dispositivo disporrà del motore di Google.

### <a name="step-2---finding-the-languages-available"></a>Passaggio 2: individuare le lingue disponibili

Il `Java.Util.Locale` classe contiene un utile metodo denominato `GetAvailableLocales()`. Questo elenco di lingue supportate dal motore di riconoscimento vocale può essere testato con le lingue installate.

È un'operazione banale per generare l'elenco di lingue "riconosciuto". Sarà sempre presente una lingua predefinita (l'utente impostato durante l'installazione prima di tutto il dispositivo di linguaggio), quindi in questo esempio il `List<string>` è "Default" come primo parametro, il resto dell'elenco verrà compilato in base al risultato del `textToSpeech.IsLanguageAvailable(locale)`.

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

Questo codice viene chiamato [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) per verificare se il language pack per le impostazioni locali specificato è già presente nel dispositivo. Questo metodo restituisce un [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), che indica se la lingua per le impostazioni locali passata è disponibile. Se `LanguageAvailableResult` indica che la lingua è `NotSupported`, non esiste alcun pacchetto voice disponibile (anche per il download) per tale lingua. Se `LanguageAvailableResult` è impostata su `MissingData`, è possibile scaricare un nuovo pacchetto di lingua, come illustrato di seguito nel passaggio 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Passaggio 3: impostare la velocità e tono

Android consente all'utente di modificare il suono del contenuto vocale modificando la `SpeechRate` e `Pitch` (la frequenza di velocità e il tono del contenuto vocale). Questo va da 0 a 1, con la voce "normale" da 1 per entrambi.

### <a name="step-4---testing-and-loading-new-languages"></a>Passaggio 4: test e il caricamento di nuovi linguaggi

Download di un nuovo linguaggio viene eseguito usando un `Intent`. Il risultato di questa finalità fa sì che il [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) metodo da richiamare. A differenza dell'esempio di riconoscimento vocale (che utilizzato il [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) come una `PutExtra` parametro per il `Intent`), il test e il caricamento `Intent`sono `Action`-basato su:

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash; un'attività viene avviata dalla piattaforma `TextToSpeech` motore per verificare la corretta installazione e la disponibilità delle risorse della lingua del dispositivo.

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash; avvia un'attività che richiede all'utente di scaricare le lingue necessarie.

Esempio di codice seguente illustra come usare queste azioni di test per le risorse della lingua e scaricare un nuovo linguaggio:

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

`TextToSpeech.Engine.ActionCheckTtsData` verifica la disponibilità di risorse della lingua. `OnActivityResult` viene richiamato al completamento di questo test. Se è necessario scaricare, le risorse della lingua `OnActivityResult` attiva il `TextToSpeech.Engine.ActionInstallTtsData` azione per avviare un'attività che consente all'utente di scaricare le lingue necessarie. Si noti che questo `OnActivityResult` implementazione non verifica il `Result` codice perché, in questo esempio semplificato, la determinazione sia già stata eseguita che è necessario scaricare il language pack.

Il `TextToSpeech.Engine.ActionInstallTtsData` cause azione il **i dati vocali Google TTS** attività devono essere presentati all'utente per la scelta delle lingue da scaricare:

![Attività di Data di sintesi vocale di Google Voice](speech-images/01-google-tts-voice-data.png)

Ad esempio, l'utente potrebbe scegliere francese e fare clic sull'icona di download per scaricare i dati vocali francese:

![Esempio di download in lingua francese](speech-images/02-selecting-french.png)

Installazione di questi dati viene eseguita automaticamente dopo aver completato il download.


### <a name="step-5---the-ioninitlistener"></a>Passaggio 5: il IOnInitListener

Per un'attività sia in grado di convertire il sintesi vocale, il metodo di interfaccia `OnInit` deve essere implementata (questo è il secondo parametro specificato per la creazione dell'istanza di `TextToSpeech` classe). Ciò consente di inizializzare il listener e si testa il risultato.

Il listener deve verificare la presenza di entrambe `OperationResult.Success` e `OperationResult.Failure` minimo.
Nell'esempio seguente illustra questa:

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

In questa guida è stato illustrato le nozioni di base della conversione di sintesi vocale e sintesi vocale in testo e metodi possibili come includerli nelle proprie app. Mentre non coprono ogni caso particolare, è stata acquisita una conoscenza di base di come viene interpretata vocale, come installare nuovi linguaggi e come aumentare la inclusivity delle tue app.



## <a name="related-links"></a>Collegamenti correlati

- [Xamarin. Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Sintesi vocale (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Riconoscimento vocale (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Spazio dei nomi Android. Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Spazio dei nomi Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
