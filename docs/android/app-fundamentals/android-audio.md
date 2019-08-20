---
title: Audio Android
description: Il sistema operativo Android offre un supporto completo per i contenuti multimediali, che comprendono audio e video. Questa guida è incentrata sull'audio in Android e descrive la riproduzione e la registrazione di audio con le classi di registratore audio e registratore audio predefinite, nonché l'API audio di basso livello. Illustra anche l'uso di eventi audio trasmessi da altre applicazioni, in modo che gli sviluppatori possano compilare applicazioni ben funzionanti.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 256871fd225808af1fdebf14c4eb2b43e575e105
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644345"
---
# <a name="android-audio"></a>Audio Android

_Il sistema operativo Android offre un supporto completo per i contenuti multimediali, che comprendono audio e video. Questa guida è incentrata sull'audio in Android e descrive la riproduzione e la registrazione di audio con le classi di registratore audio e registratore audio predefinite, nonché l'API audio di basso livello. Illustra anche l'uso di eventi audio trasmessi da altre applicazioni, in modo che gli sviluppatori possano compilare applicazioni ben funzionanti._


## <a name="overview"></a>Panoramica

I dispositivi mobili moderni hanno adottato funzionalità che in precedenza avrebbero richiesto parti dedicate di fotocamere di apparecchiature &ndash; , lettori musicali e registratori di video. Per questo motivo, i framework multimediali sono diventati una funzionalità di prima classe nelle API per dispositivi mobili.

Android offre un supporto completo per i contenuti multimediali. Questo articolo esamina l'uso dell'audio in Android e illustra gli argomenti seguenti

1.  **Riproduzione di audio con MediaPlayer** Uso della `MediaPlayer` classe predefinita per riprodurre audio, inclusi i file audio locali e i file audio trasmessi con la `AudioTrack` classe. &ndash;

2.  **Registrazione audio** Uso della `MediaRecorder` classe predefinita per registrare l'audio. &ndash;

3.  **Uso delle notifiche audio** &ndash; Usare le notifiche audio per creare applicazioni ben funzionanti che rispondono correttamente agli eventi, ad esempio le chiamate telefoniche in ingresso, tramite la sospensione o l'annullamento degli output audio.

4.  **Utilizzo di audio di basso livello** Riproduzione di audio tramite `AudioTrack` la classe scrivendo direttamente nei buffer di memoria. &ndash; Registrare l'audio usando `AudioRecord` la classe e leggendo direttamente dai buffer di memoria.


## <a name="requirements"></a>Requisiti

Questa guida richiede Android 2,0 (livello API 5) o versione successiva. Si noti che il debug di audio in Android deve essere eseguito in un dispositivo.

È necessario richiedere le `RECORD_AUDIO` autorizzazioni in **file AndroidManifest. XML**:

![Sezione autorizzazioni necessarie del manifesto Android con registrazione\_audio abilitata](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Riproduzione di audio con la classe MediaPlayer

Il modo più semplice per riprodurre audio in Android è la classe [MediaPlayer](xref:Android.Media.MediaPlayer) incorporata.
`MediaPlayer`consente di riprodurre file locali o remoti passando il percorso del file. Tuttavia, `MediaPlayer` è molto sensibile allo stato e la chiamata di uno dei relativi metodi nello stato errato causerà la generazione di un'eccezione. È importante interagire con `MediaPlayer` nell'ordine descritto di seguito per evitare errori.



### <a name="initializing-and-playing"></a>Inizializzazione e riproduzione

Per la riproduzione `MediaPlayer` di audio con è richiesta la sequenza seguente:

1. Creare un'istanza di un nuovo oggetto [MediaPlayer](xref:Android.Media.MediaPlayer) .

1. Configurare il file per la riproduzione tramite il metodo [SetDataSource](xref:Android.Media.MediaPlayer.SetDataSource*).

1. Chiamare il metodo [Prepare](xref:Android.Media.MediaPlayer.Prepare) per inizializzare il lettore.

1. Chiamare il metodo [Start](xref:Android.Media.MediaPlayer.Start) per avviare la riproduzione audio.


Nell'esempio di codice riportato di seguito viene illustrato questo utilizzo:

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>Sospensione e ripresa della riproduzione

La riproduzione può essere sospesa chiamando il metodo [pause](xref:Android.Media.MediaPlayer.Pause) :

```csharp
player.Pause();
```

Per riprendere la riproduzione sospesa, chiamare il metodo [Start](xref:Android.Media.MediaPlayer.Start) .
Questa operazione verrà ripresa dal percorso sospeso nella riproduzione:

```csharp
player.Start();
```

La chiamata al metodo [Stop](xref:Android.Media.MediaPlayer.Stop) sul lettore termina una riproduzione in corso:

```csharp
player.Stop();
```

Quando il lettore non è più necessario, le risorse devono essere rilasciate chiamando il metodo di [rilascio](xref:Android.Media.MediaPlayer.Release) :

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Uso della classe MediaRecorder per registrare l'audio

Il corollario `MediaPlayer` di per la registrazione di audio in Android è la classe [MediaRecorder](xref:Android.Media.MediaRecorder) . `MediaPlayer`Come, è sensibile allo stato e passa attraverso diversi stati per arrivare al punto in cui è possibile avviare la registrazione. Per registrare l'audio, è necessario `RECORD_AUDIO` impostare l'autorizzazione. Per istruzioni su come impostare le autorizzazioni dell'applicazione, vedere [utilizzo di file AndroidManifest. XML](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Inizializzazione e registrazione

Per registrare l' `MediaRecorder` audio con, è necessario eseguire i passaggi seguenti:

1. Creare un'istanza di un nuovo oggetto [MediaRecorder](xref:Android.Media.MediaRecorder) .

2. Specificare quale dispositivo hardware usare per acquisire l'input audio tramite il metodo [SetAudioSource](xref:Android.Media.MediaRecorder.SetAudioSource*) .

3. Impostare il formato audio del file di output usando il metodo [SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*) . Per un elenco dei tipi di audio supportati, vedere [formati multimediali supportati da Android](https://developer.android.com/guide/appendix/media-formats.html).

4. Chiamare il metodo [SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*) per impostare il tipo di codifica audio.

5. Chiamare il metodo [SetOutputFile](xref:Android.Media.MediaRecorder.SetOutputFile*) per specificare il nome del file di output in cui vengono scritti i dati audio.

6. Chiamare il metodo [Prepare](xref:Android.Media.MediaRecorder.Prepare) per inizializzare il registratore.

7. Chiamare il metodo [Start](xref:Android.Media.MediaRecorder.Start) per avviare la registrazione.


Nell'esempio di codice seguente viene illustrata questa sequenza:

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>Arresto della registrazione

Per arrestare la registrazione, chiamare il `Stop` metodo `MediaRecorder`su:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Pulizia

Una volta arrestato `MediaRecorder`, chiamare il metodo [Reset](xref:Android.Media.MediaRecorder.Reset) per riportarlo nello stato inattivo:

```csharp
recorder.Reset();
```

Quando non `MediaRecorder` è più necessario, le relative risorse devono essere rilasciate chiamando il metodo di [rilascio](xref:Android.Media.MediaRecorder.Release) :

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Gestione delle notifiche audio



### <a name="the-audiomanager-class"></a>Classe AudioManager

La classe [AudioManager](xref:Android.Media.AudioManager) fornisce l'accesso alle notifiche audio che consentono alle applicazioni di stabilire quando si verificano eventi audio. Questo servizio fornisce anche l'accesso ad altre funzionalità audio, ad esempio il controllo della modalità volume e suoneria. Consente `AudioManager` a un'applicazione di gestire le notifiche audio per controllare la riproduzione audio.



### <a name="managing-audio-focus"></a>Gestione dello stato attivo dell'audio

Le risorse audio del dispositivo (il lettore e il registratore predefiniti) sono condivise da tutte le applicazioni in esecuzione.

Dal punto di vista concettuale, questo è simile alle applicazioni in un computer desktop in cui solo un'applicazione ha lo stato attivo: dopo aver selezionato una delle applicazioni in esecuzione facendo clic con il mouse su di essa, l'input da tastiera passa solo a tale applicazione.

Lo stato attivo è un'idea simile e impedisce a più di un'applicazione di riprodurre o registrare l'audio nello stesso momento. È più complicato dello stato attivo perché è volontario &ndash; l'applicazione può ignorare il fatto che attualmente non ha lo stato attivo e la riproduzione audio &ndash; indipendentemente e perché sono disponibili tipi diversi di stato attivo che può essere richiesto. Se, ad esempio, il richiedente deve riprodurre l'audio solo per un breve periodo di tempo, potrebbe richiedere lo stato attivo temporaneo.

Lo stato attivo audio può essere concesso immediatamente o inizialmente negato e concesso in un secondo momento. Se, ad esempio, un'applicazione richiede lo stato attivo durante una telefonata, viene negato, ma lo stato attivo può essere concesso dopo che la telefonata è stata completata. In questo caso, viene registrato un listener per rispondere di conseguenza se lo stato attivo viene sottratto. La richiesta di attivazione dell'audio viene utilizzata per determinare se è accettabile o meno riprodurre l'audio.

Per altre informazioni sullo stato attivo, vedere [gestione dello stato attivo](https://developer.android.com/training/managing-audio/audio-focus.html)dell'audio.



#### <a name="registering-the-callback-for-audio-focus"></a>Registrazione del callback per lo stato attivo audio

La registrazione del `FocusChangeListener` callback `IOnAudioChangeListener` da è una parte importante del recupero e del rilascio dello stato attivo. Ciò è dovuto al fatto che la concessione della messa a fuoco dell'audio può essere rinviata fino a un secondo momento. Ad esempio, un'applicazione può richiedere la riproduzione di musica mentre è in corso una telefonata. Lo stato attivo non verrà concesso finché non viene completata la telefonata.

Per questo motivo, l'oggetto callback viene passato come parametro nel `GetAudioFocus` metodo `AudioManager`di ed è questa chiamata che registra il callback. Se lo stato attivo viene inizialmente negato, ma `OnAudioFocusChange` in seguito concesso, l'applicazione viene informata richiamando nel callback. Lo stesso metodo viene usato per indicare all'applicazione che lo stato attivo viene sottratto.

Quando l'applicazione ha terminato di usare le risorse audio, chiama il `AbandonFocus` metodo dell'oggetto `AudioManager`e passa di nuovo nel callback. Questa operazione Annulla la registrazione del callback e rilascia le risorse audio, in modo che altre applicazioni possano ottenere lo stato attivo.



#### <a name="requesting-audio-focus"></a>Richiesta dello stato attivo dell'audio

I passaggi necessari per richiedere le risorse audio del dispositivo sono i seguenti:

1.  Ottenere un handle per il `AudioManager` servizio di sistema.

2.  Creare un'istanza della classe di callback.

3.  Richiedere le risorse audio del dispositivo chiamando il `RequestAudioFocus` metodo `AudioManager` su. I parametri sono l'oggetto callback, il tipo di flusso (musica, chiamata vocale, anello e così via) e il tipo di diritto di accesso richiesto (le risorse audio possono essere richieste momentaneamente o per un periodo indefinito, ad esempio).

4.  Se la richiesta viene concessa, `playMusic` il metodo viene richiamato immediatamente e l'audio inizia a riprodursi.

5.  Se la richiesta viene negata, non viene eseguita alcuna azione ulteriore. In questo caso, l'audio verrà riprodotto solo se la richiesta viene concessa in un secondo momento.


Nell'esempio di codice riportato di seguito vengono illustrati i passaggi seguenti:

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>Rilascio dello stato attivo dell'audio

Al termine della riproduzione della traccia, viene richiamato `AbandonFocus` il metodo `AudioManager` su. Questo consente a un'altra applicazione di ottenere le risorse audio del dispositivo. Altre applicazioni riceveranno una notifica di questo cambiamento dello stato attivo se hanno registrato i propri listener.


## <a name="low-level-audio-api"></a>API audio di basso livello

Le API audio di basso livello offrono un maggiore controllo sulla riproduzione e la registrazione audio, perché interagiscono direttamente con i buffer di memoria anziché con gli URI di file. Esistono scenari in cui questo approccio è preferibile. Questi scenari includono:

1.  Quando si esegue la riproduzione da file audio crittografati.

2.  Quando si riproduce una sequenza di brevi clip.

3.  Streaming audio.


### <a name="audiotrack-class"></a>Classe traccia audio

La classe [traccia audio](xref:Android.Media.AudioTrack) usa le API audio di basso livello per la registrazione ed è l'equivalente di basso livello della `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>Inizializzazione e riproduzione

Per riprodurre l'audio, è necessario creare `AudioTrack` un'istanza di una nuova istanza di. L'elenco di argomenti passato nel [Costruttore](xref:Android.Media.AudioTrack) specifica come riprodurre l'esempio audio contenuto nel buffer. Gli argomenti sono:

1.  Tipo &ndash; di flusso voce, suoneria, musica, sistema o allarme.

2.  Frequenza &ndash; della frequenza di campionamento espressa in Hz.

3.  Configurazione &ndash; del canale mono o stereo.

4.  Codifica in &ndash; formato audio a 8 bit o a 16 bit.

5.  Dimensioni &ndash; del buffer in byte.

6.  Flusso o &ndash; statico in modalità buffer.


Dopo la costruzione, viene richiamato il metodo [Play](xref:Android.Media.AudioTrack.Play) di `AudioTrack` per configurarlo per avviare la riproduzione. La scrittura del buffer audio in `AudioTrack` avvia la riproduzione:

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>Sospensione e arresto della riproduzione

Chiamare il metodo [pause](xref:Android.Media.AudioTrack.Pause) per sospendere la riproduzione:

```csharp
audioTrack.Pause();
```

Se si chiama il metodo [Stop](xref:Android.Media.AudioTrack.Stop) , la riproduzione viene terminata in modo permanente:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Pulizia

Quando non `AudioTrack` è più necessario, le relative risorse devono essere rilasciate chiamando il [rilascio](xref:Android.Media.AudioTrack.Release):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>Classe AudioRecord

La classe [AudioRecord](xref:Android.Media.AudioRecord) è l'equivalente di `AudioTrack` sul lato della registrazione. Come `AudioTrack`, USA i buffer di memoria direttamente, al posto di file e URI. Richiede l'impostazione dell' `RECORD_AUDIO` autorizzazione nel manifesto.


#### <a name="initializing-and-recording"></a>Inizializzazione e registrazione

Il primo passaggio consiste nel creare un nuovo oggetto [AudioRecord](xref:Android.Media.AudioRecord) . L'elenco di argomenti passato nel [Costruttore](xref:Android.Media.AudioRecord) fornisce tutte le informazioni necessarie per la registrazione. Diversamente da, `AudioTrack`in cui gli argomenti sono enumerazioni di grandi dimensioni, gli argomenti equivalenti in `AudioRecord` sono numeri interi. Sono inclusi:

1.  Origine di input audio hardware, ad esempio microfono.

2.  Tipo &ndash; di flusso voce, suoneria, musica, sistema o allarme.

3.  Frequenza &ndash; della frequenza di campionamento espressa in Hz.

4.  Configurazione &ndash; del canale mono o stereo.

5.  Codifica in &ndash; formato audio a 8 bit o a 16 bit.

6.  Dimensioni del buffer: in byte


Una volta `AudioRecord` costruito l'oggetto, viene richiamato il metodo [StartRecording](xref:Android.Media.AudioRecord.StartRecording) . È ora possibile iniziare la registrazione. Legge `AudioRecord` continuamente il buffer audio per l'input e scrive questo input in un file audio.

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>Arresto della registrazione

La chiamata al metodo [Stop](xref:Android.Media.AudioRecord.Stop) termina la registrazione:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Pulizia

Quando l' `AudioRecord` oggetto non è più necessario, la chiamata al metodo di [rilascio](xref:Android.Media.AudioRecord.Release) rilascia tutte le risorse associate:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Riepilogo

Il sistema operativo Android fornisce un Framework potente per la riproduzione, la registrazione e la gestione di audio. Questo articolo ha illustrato come riprodurre e registrare l'audio usando le classi di `MediaPlayer` alto `MediaRecorder` livello e. Successivamente, si è esplorato come usare le notifiche audio per condividere le risorse audio del dispositivo tra applicazioni diverse. Infine, ha illustrato come riprodurre e registrare l'audio usando le API di basso livello, che si interfacciano direttamente con i buffer di memoria.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di audio (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [Media Player](xref:Android.Media.MediaPlayer)
- [Registratore multimediale](xref:Android.Media.MediaRecorder)
- [Audio Manager](xref:Android.Media.AudioManager)
- [Traccia audio](xref:Android.Media.AudioTrack)
- [Registratore audio](xref:Android.Media.AudioRecord)
