---
title: Riconoscimento vocale Android
description: Questo articolo vengono illustrate le nozioni di base dell'utilizzo di spazio dei nomi Android.Speech molto potente. Dall'inizio, Android è stato in grado di riconoscimento vocale e di eseguirne l'output come testo. È un processo relativamente semplice. Per sintesi vocale, tuttavia, il processo è più complesso, perché non solo il motore di riconoscimento vocale deve essere prese in considerazione, ma anche le lingue disponibili e installate dal sistema di sintesi vocale (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/02/2018
ms.openlocfilehash: bdaa9bf09485c06551a2df15a2e3a4b410a53e75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-speech"></a>Riconoscimento vocale Android

_Questo articolo vengono illustrate le nozioni di base dell'utilizzo di spazio dei nomi Android.Speech molto potente. Dall'inizio, Android è stato in grado di riconoscimento vocale e di eseguirne l'output come testo. È un processo relativamente semplice. Per sintesi vocale, tuttavia, il processo è più complesso, perché non solo il motore di riconoscimento vocale deve essere prese in considerazione, ma anche le lingue disponibili e installate dal sistema di sintesi vocale (TTS)._

## <a name="speech-overview"></a>Panoramica di riconoscimento vocale

Con un sistema, quale "riconosce" voce umana ed enunciates quanto digitato, riconoscimento vocale per testo e di sintesi vocale, è un'area crescente all'interno di sviluppo per dispositivi mobile quando aumenta la richiesta per la comunicazione naturale con i dispositivi. Sono presenti molte istanze in presenza di una funzionalità che converte il testo nel riconoscimento vocale o viceversa, è uno strumento estremamente utile per incorporare nell'applicazione android.

Ad esempio, con clamp verso il basso al telefono cellulare utilizzo durante la Guida, gli utenti desiderano un modo libero mani operativo i propri dispositivi. L'insieme di fattori di forma Android diversi, ad esempio, usare Android, e l'inclusione sempre più ampio di quelle in grado di utilizzare i dispositivi Android (ad esempio Tablet e riempie nota), ha creato un obiettivo più grande in applicazioni di sintesi vocale.

Google fornisce allo sviluppatore una vasta gamma di API nello spazio dei nomi Android.Speech per coprire la maggior parte delle istanze di rendere i dispositivi "vocale compatibile con" (ad esempio un software progettato for the blind).  Lo spazio dei nomi include la possibilità di consentire che il testo da convertire in vocale tramite `Android.Speech.Tts`, il motore usato per eseguire la traduzione, nonché un numero di controllo `RecognizerIntent`s che consentono di riconoscimento vocale da convertire in testo.

Mentre le funzionalità sono presenti per i dialoghi di comprendere, sono previste limitazioni in base all'hardware utilizzato. È improbabile che il dispositivo verrà interpretato correttamente tutti gli elementi si parla in tutte le lingue disponibili.

## <a name="requirements"></a>Requisiti

Non sono previsti requisiti speciali di questa Guida, diverso da un dispositivo che presenta un microfono e degli altoparlanti.

Alla base di un dispositivo Android interpretazione vocale è l'utilizzo di un `Intent` con un corrispondente `OnActivityResult`.
È importante, tuttavia, ricordare che il riconoscimento vocale non è stata riconosciuta, ma interpretato al testo. La differenza è importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La differenza tra la comprensione e l'interpretazione

Una semplice definizione di comprensione è che si è in grado di determinare il significato di ciò che viene detto reale tono e contesto. Per interpretare appena si intende accettare i termini e restituirli in un altro modulo.

Si consideri il semplice esempio seguente viene utilizzato nella conversazione quotidiane: 

<kbd>Ciao come stai?</kbd>

Assenza di curvatura (particolare importanza in parole specifiche o parti di parole), è una semplice domanda. Tuttavia, se un ritmo lento viene applicato alla riga, la persona ascolto rileverà richiedente che non sia troppo lieta forse deve Grandissimo o che il richiedente è unwell. Se viene inserito l'enfasi on "sono", la persona che richiede è in genere più interessata nella risposta.

Senza audio sufficientemente potente elaborazione per rendere l'uso di curvatura e un livello di intelligenza artificiale (AI) per capire il contesto, il software non è possibile anche iniziare a comprendere cosa è stata definita, ottimali un telefono semplice è il riconoscimento vocale anche convertire testo.

## <a name="setting-up"></a>Impostazione

Prima di utilizzare il sistema di riconoscimento vocale, è sempre consigliabile verificare che il dispositivo dispone di un microfono. Non vi sarà inutile sta tentando di eseguire l'app in un blocco note Kindle o Google senza un microfono installato.

Esempio di codice seguente illustra l'esecuzione di query se il microfono sia disponibile e in caso contrario, per creare un avviso. Se non è disponibile alcun microfono a questo punto si sarebbe chiudere l'attività o disabilitare la possibilità di registrare il riconoscimento vocale.

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

Lo scopo per il sistema di riconoscimento vocale utilizza un tipo particolare di finalità chiamato il `RecognizerIntent`. Questa finalità controlla un numero elevato di parametri, inclusi il tempo di attesa con inattività fino a quando non viene considerata la registrazione su, tutte le lingue aggiuntive per riconoscere e di output e il testo da includere nel `Intent`della finestra di dialogo modale come mezzo di istruzione. In questo frammento, `VOICE` è un `readonly int` utilizzato per il riconoscimento in `OnActivityResult`.

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

Il testo interpretato del contenuto vocale verrà recapitato all'interno di `Intent`, che viene restituito quando l'attività è stata completata e si accede tramite `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Verrà restituito un `IList<string>`, di cui l'indice può essere utilizzato e visualizzato, a seconda del numero di lingue richiesto con lo scopo del chiamante (e specificato il `RecognizerIntent.ExtraMaxResults`). Come con qualsiasi elenco, tuttavia, è opportuno un controllo per verificare che sia possibile visualizzare i dati.

Durante l'attesa per il valore restituito di un `StartActivityForResult`, `OnActivityResult` metodo deve essere fornito.

Nell'esempio seguente, `textBox` è un `TextBox` utilizzato per l'output dei quali è stato determinato. Potrebbe anche essere utilizzata per passare il testo in una forma dell'interprete e da lì, l'applicazione è possibile confrontare il testo e un ramo a un'altra parte dell'applicazione.

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
                  switch(matches[0].Substring(0,5).ToLower())
                  {
                     case "north":
                      MovePlayer(0);
                     break;
                   case "south":
                     MovePlayer(1);
                     break;
             }
             else
                  textBox.Text = "No speech was recognised";
        }
   }
    base.OnActivityResult(requestCode, resultVal, data);
}
```

## <a name="text-to-speech"></a>Sintesi vocale

Sintesi vocale non è piuttosto l'opposto di riconoscimento vocale per testo e si basa su due componenti principali; viene installato un motore di sintesi vocale sul dispositivo e una lingua da installare.

In gran parte, i dispositivi Android forniti con il valore predefinito installato servizio Google TTS e almeno una lingua. Ciò viene stabilito quando il dispositivo prima di tutto configurare e sarà basato su cui il dispositivo non è al momento (ad esempio, un telefono configurato in Germania installerà la lingua tedesca, mentre uno in America avrà inglese americano).

### <a name="step-1---instantiating-texttospeech"></a>Passaggio 1: creare un'istanza TextToSpeech

`TextToSpeech` può richiedere fino a 3 parametri, le prime due sono obbligatori mentre il terzo facoltativa (`AppContext`, `IOnInitListener`, `engine`). Il listener viene utilizzato per associare il servizio e il test per un errore con il motore da qualsiasi numero di motori disponibile Android sintesi vocale. Come minimo, il dispositivo disporrà di motore di Google.

### <a name="step-2---finding-the-languages-available"></a>Passaggio 2: individuare le lingue disponibili

Il `Java.Util.Locale` classe contiene un metodo utile denominato `GetAvailableLocales()`. Questo elenco di lingue supportate dal motore di riconoscimento vocale può essere testato le lingue installate.

È un'operazione semplice per generare l'elenco di lingue "riconosciuto". Sarà sempre presente una lingua predefinita (l'utente impostato durante l'installazione prima il dispositivo di linguaggio), pertanto in questo esempio il `List<string>` è "Default" come primo parametro, il resto dell'elenco verrà compilato in base al risultato del `textToSpeech.IsLanguageAvailable(locale)`.

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

Questo codice chiama [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) per verificare se il language pack per impostazioni locali specificato è già presente nel dispositivo. Questo metodo restituisce un [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), che indica se è disponibile la lingua per le impostazioni locali passata. Se `LanguageAvailableResult` indica che la lingua è `NotSupported`, non esiste alcun pacchetto vocale (anche per il download) per tale lingua. Se `LanguageAvailableResult` è impostata su `MissingData`, è possibile scaricare un nuovo pacchetto di lingua, come illustrato di seguito nel passaggio 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Passaggio 3: impostazione della velocità e tono

Android consente all'utente di modificare il suono del riconoscimento vocale modificando il `SpeechRate` e `Pitch` (il tasso di velocità e il tono del riconoscimento vocale). Questo va da 0 a 1, con la voce "normale" da 1 per entrambi.

### <a name="step-4---testing-and-loading-new-languages"></a>Passaggio 4: test e il caricamento di nuove lingue

Download di una nuova lingua viene eseguito usando un `Intent`. Il risultato di questa finalità fa sì che il [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) metodo da richiamare. A differenza dell'esempio discorso in testo (che il [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) come una `PutExtra` parametro per il `Intent`), il testing e il caricamento `Intent`sono `Action`-basato su:

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash; avvia un'attività dalla piattaforma `TextToSpeech` motore per verificare la corretta installazione e la disponibilità delle risorse di lingua del dispositivo.

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash; avvia un'attività che richiede all'utente di scaricare le lingue necessarie.

Esempio di codice seguente viene illustrato come usare queste azioni per verificare la presenza di risorse di lingua e scaricare un nuovo linguaggio:

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

`TextToSpeech.Engine.ActionCheckTtsData` verifica la disponibilità di risorse di lingua. `OnActivityResult` viene richiamato al completamento di questo test. Se è necessario scaricare, le risorse della lingua `OnActivityResult` genera il `TextToSpeech.Engine.ActionInstallTtsData` per avviare un'attività che consente all'utente di scaricare le lingue necessarie. Nota che questo `OnActivityResult` implementazione non controlla il `Result` codice, in quanto, in questo esempio semplificato, la determinazione è già stata creata che è necessario scaricare il language pack.

Il `TextToSpeech.Engine.ActionInstallTtsData` vengono illustrate le cause di azione il **dati vocali Google TTS** attività vengano presentate all'utente per la scelta delle lingue da scaricare:

![Google sintesi vocale dati attività](speech-images/01-google-tts-voice-data.png)

Ad esempio, l'utente potrebbe selezionare francese e fare clic sull'icona di download per scaricare dati vocali francese:

![Esempio di download in lingua francese](speech-images/02-selecting-french.png)

Installazione di questi dati viene eseguita automaticamente una volta completato il download.


### <a name="step-5---the-ioninitlistener"></a>Passaggio 5 - il IOnInitListener

Per un'attività in grado di convertire il sintesi vocale, il metodo di interfaccia `OnInit` deve essere implementato (questo è il secondo parametro specificato per la creazione dell'istanza di `TextToSpeech` classe). Inizializza il listener e il risultato di test.

Il listener è necessario testare sia per `OperationResult.Success` e `OperationResult.Failure` minimo.
L'esempio seguente mostra solo che:

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

In questa guida sono state osservate le nozioni di base della conversione di sintesi e riconoscimento vocale per testo e i possibili metodi di includerli all'interno delle applicazioni. Mentre non coprono ogni caso particolare, è stata acquisita una conoscenza di base delle modalità di interpretazione di riconoscimento vocale sull'installazione di nuove lingue e come aumentare inclusivity delle app.



## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Sintesi vocale (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Riconoscimento vocale per testo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Spazio dei nomi Android.Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Spazio dei nomi Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
