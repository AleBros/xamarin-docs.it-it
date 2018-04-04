---
title: Android Audio
description: Il sistema operativo Android fornisce supporto completo per servizi multimediali che comprende sia audio e video. Questa guida è incentrata sulla audio in Android e copre riprodurre e registrare audio tramite lettori audio incorporati e le classi di registrazione, nonché l'API audio di basso livello. Utilizzo degli eventi Audio broadcast da altre applicazioni, vengono inoltre illustrate in modo che gli sviluppatori possono compilare applicazioni ben progettate.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: aff0d67549707129bfc85246318c33c522e4f1f6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-audio"></a>Android Audio

_Il sistema operativo Android fornisce supporto completo per servizi multimediali che comprende sia audio e video. Questa guida è incentrata sulla audio in Android e copre riprodurre e registrare audio tramite lettori audio incorporati e le classi di registrazione, nonché l'API audio di basso livello. Utilizzo degli eventi Audio broadcast da altre applicazioni, vengono inoltre illustrate in modo che gli sviluppatori possono compilare applicazioni ben progettate._


## <a name="overview"></a>Panoramica

I dispositivi mobili moderni hanno adottato funzionalità che in precedenza sarebbe necessario dedicati componenti &ndash; videocamere, lettori di musica e i masterizzatori di video. Per questo motivo, Framework multimediali sono diventati una funzionalità di prima classe di API per dispositivi mobili.

Android fornisce supporto completo per file multimediali. In questo articolo esamina l'utilizzo di audio in Android e vengono trattati i seguenti argomenti

1.  **Riproduzione di Audio con MediaPlayer** &ndash; utilizzando l'elemento predefinito `MediaPlayer` classe per riprodurre l'audio, inclusi i file audio locali e con flusso audio con la `AudioTrack` classe.

2.  **Registrare l'Audio** &ndash; utilizzando l'elemento predefinito `MediaRecorder` classe di registrare l'audio.

3.  **Utilizzo di notifiche Audio** &ndash; tramite notifiche audio per creare applicazioni ben progettate che rispondono a eventi (ad esempio chiamate telefoniche in ingresso) sospensione o l'annullamento relativi output audio.

4.  **Utilizzo di basso livello Audio** &ndash; la riproduzione di audio usando la `AudioTrack` classe scrivendo direttamente al buffer di memoria. La registrazione audio usando la `AudioRecord` classe e la lettura direttamente dai buffer di memoria.


## <a name="requirements"></a>Requisiti

Questa guida richiede Android 2.0 (API livello 5) o versione successiva. Si noti che è necessario eseguire il debug di audio in Android in un dispositivo.

È necessario eseguire una richiesta di `RECORD_AUDIO` autorizzazioni in **AndroidManifest.XML**:

![Sezione autorizzazioni di manifesto Android con RECORD necessari\_AUDIO abilitato](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Riproduzione di Audio con la classe di Media Player

È il modo più semplice per riprodurre l'audio in Android con predefinito [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) classe.
`MediaPlayer` possibile riprodurre un file locali o remoti passando il percorso del file. Tuttavia, `MediaPlayer` è molto sensibili dello stato e la chiamata a uno dei metodi nello stato errato genererà un'eccezione generata. È importante interagire con `MediaPlayer` nell'ordine descritto di seguito per evitare errori.



### <a name="initializing-and-playing"></a>L'inizializzazione e la riproduzione

Riproduzione di audio con `MediaPlayer` richiede la seguente sequenza:

1. Creare un'istanza di un nuovo [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) oggetto.

1. Configurare il file da riprodurre tramite il [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) metodo.

1. Chiamare il [Prepare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) metodo per inizializzare il lettore.

1. Chiamare il [avviare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/) metodo per avviare la riproduzione di audio.


Esempio di codice seguente viene illustrato l'utilizzo:

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

La riproduzione può essere sospeso chiamando il [pausa](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) metodo:

```csharp
player.Pause();
```

Per riprendere la riproduzione sospesa, chiamare il [avviare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) metodo.
Questa attività riprenderà dalla posizione in pausa la riproduzione:

```csharp
player.Start();
```

La chiamata di [arrestare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) metodo del lettore termina una riproduzione in corso:

```csharp
player.Stop();
```

Quando il lettore non è più necessario, le risorse devono essere rilasciate chiamando il [versione](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) metodo:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Utilizzare la classe MediaRecorder per Record Audio

Il corollario `MediaPlayer` per registrare l'audio in Android è il [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) classe. Ad esempio il `MediaPlayer`, è stato sensibili e passa attraverso diversi stati, per ottenere il punto in cui è possibile avviare la registrazione. Per registrare l'audio, di `RECORD_AUDIO` devono essere impostate. Per istruzioni su come impostare l'applicazione autorizzazioni, vedere [utilizzo AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>L'inizializzazione e la registrazione

La registrazione dell'audio con il `MediaRecorder` richiede i passaggi seguenti:

1. Creare un'istanza di un nuovo [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) oggetto.

2. Specificare quale dispositivo hardware da usare per acquisire l'input audio tramite il [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) metodo.

3. Impostare il formato audio file di output utilizzando il [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) metodo. Per un elenco di tipi di audio supportati, vedere [Android Supported Media Formats](http://developer.android.com/guide/appendix/media-formats.html).

4. Chiamare il [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) per impostare il tipo di codifica audio.

5. Chiamare il [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) metodo per specificare il nome del file di output scritti i dati audio.

6. Chiamare il [Prepare](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) metodo per inizializzare la registrazione.

7. Chiamare il [avviare](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) metodo per avviare la registrazione.


L'esempio di codice seguente viene illustrata questa sequenza:

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


### <a name="stopping-recording"></a>L'arresto di registrazione

Per arrestare la registrazione, chiamare il `Stop` metodo il `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Pulizia

Una volta il `MediaRecorder` è stato arrestato, chiamare il [reimpostare](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) metodo per renderlo nuovamente nello stato di inattività:

```csharp
recorder.Reset();
```

Quando il `MediaRecorder` è più necessario, le relative risorse devono essere liberate chiamando il [versione](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) (metodo):

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>La gestione delle notifiche Audio



### <a name="the-audiomanager-class"></a>La classe AudioManager

Il [AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) classe fornisce accesso alle notifiche audio che consentono alle applicazioni di sapere quando si verificano eventi audio. Questo servizio fornisce inoltre l'accesso ad altre funzionalità audio, ad esempio volumi e suoneria controllo modalità. Il `AudioManager` consente a un'applicazione gestire le notifiche audio per controllare la riproduzione di audio.



### <a name="managing-audio-focus"></a>Gestione degli elementi attivi Audio

Le risorse audio del dispositivo (Windows Media player incorporato e registrazione) sono condivise da tutte le applicazioni in esecuzione.

Concettualmente, è simile alle applicazioni in un computer desktop in un'unica applicazione con lo stato attivo: dopo aver selezionato una delle applicazioni in esecuzione facendo clic del mouse, l'input della tastiera passa solo a tale applicazione.

Lo stato attivo audio è un concetto simile e impedisce più di un'applicazione la riproduzione e la registrazione dell'audio nello stesso momento. È stato attivo della tastiera più complicato perché è volontaria &ndash; l'applicazione è possibile ignorare il fatto che attualmente non è attivo audio e riprodurre indipendentemente dal fatto che &ndash; e perché esistono diversi tipi di concentrazione audio che possono essere richiesto. Ad esempio, se il richiedente è previsto solo per riprodurre l'audio per un tempo molto breve, lo stato attivo temporaneo può chiedere.

Lo stato attivo audio può concedere immediatamente o inizialmente negato e concesse in un secondo momento. Ad esempio, se un'applicazione richieste audio lo stato attivo durante una chiamata telefonica, verrà negato, ma lo stato attivo può essere concesso anche al termine della chiamata telefonica. In questo caso, un listener è registrato per rispondere di conseguenza, se lo stato attivo audio non viene eseguito immediatamente. Richiede lo stato attivo audio viene utilizzato per determinare se è OK per riprodurre o registrare l'audio.

Per ulteriori informazioni sullo stato attivo audio, vedere [gestione Audio attivo](http://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Registrazione di Callback per lo stato attivo Audio

Registrazione di `FocusChangeListener` callback il `IOnAudioChangeListener` è una parte importante di recupero e il rilascio di audio attivo. Questo avviene perché la concessione di concentrazione audio può essere rinviata fino a un secondo momento. Ad esempio, un'applicazione può richiedere da riprodurre musica mentre è in corso una chiamata telefonica. Lo stato attivo audio non verrà concessa fino al completamento della chiamata telefonica.

Per questo motivo, l'oggetto di callback viene passato come parametro nel `GetAudioFocus` metodo il `AudioManager`, ed è la chiamata che esegue la registrazione del callback. Se lo stato attivo audio è inizialmente negato ma in seguito, l'applicazione viene informato richiamando `OnAudioFocusChange` nel callback. Lo stesso metodo utilizzato per indicare l'applicazione che lo stato attivo audio viene intrapresa da subito.

Quando l'applicazione ha terminato di utilizzare le risorse audio, chiama il `AbandonFocus` metodo il `AudioManager`e lo passa nuovamente nel callback. Si annulla la registrazione del callback e rilascia le risorse audio, in modo che le altre applicazioni possono ottenere lo stato attivo audio.



#### <a name="requesting-audio-focus"></a>Richiede lo stato attivo Audio

I passaggi necessari per richiedere le risorse del dispositivo audio sono i seguenti:

1.  Ottenere un handle per il `AudioManager` servizio di sistema.

2.  Creare un'istanza della classe di callback.

3.  Richiedere le risorse del dispositivo audio chiamando il `RequestAudioFocus` metodo il `AudioManager` . I parametri sono l'oggetto callback, il tipo di flusso (musica, chiamata vocale, ad anello e così via) e il tipo dell'accesso richiesto a destra (le risorse audio possono essere richiesta momentaneamente o per un periodo di tempo indefinito, ad esempio).

4.  Se la richiesta viene concesso, il `playMusic` metodo viene richiamato immediatamente e avvia l'audio da riprodurre.

5.  Se la richiesta viene negata, viene eseguita alcuna azione ulteriore. In questo caso, l'audio verrà riprodotto solo se la richiesta è stata concessa in un secondo momento.


L'esempio di codice riportato di seguito vengono illustrati tali passaggi:

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


#### <a name="releasing-audio-focus"></a>Stato attivo per il rilascio Audio

Al termine, la riproduzione della traccia di `AbandonFocus` metodo `AudioManager` viene richiamato. In questo modo un'altra applicazione ottenere le risorse del dispositivo audio. Altre applicazioni riceverà una notifica di questa modifica lo stato attivo audio se sono registrati i propri listener.


## <a name="low-level-audio-api"></a>Basso livello API di Audio

API audio di basso livello forniscono un maggiore controllo audio di riproduzione e la registrazione in quanto interagiscono direttamente con il buffer di memoria invece di utilizzare URI di file. Esistono alcuni scenari in cui questo approccio è preferibile. Tali scenari includono:

1.  Durante la riproduzione di file audio crittografati.

2.  Durante la riproduzione di una serie di brevi clip.

3.  Lo streaming di audio.


### <a name="audiotrack-class"></a>Classe di AudioTrack

Il [AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) classe Usa l'API audio di basso livello per la registrazione ed è l'equivalente di basso livello di `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>L'inizializzazione e la riproduzione

Per riprodurre l'audio, una nuova istanza della `AudioTrack` deve essere creata un'istanza. L'elenco di argomenti passato il [costruttore](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) specifica la modalità di riprodurre l'audio esempio contenuto nel buffer. Gli argomenti sono:

1.  Tipo di flusso &ndash; vocale, suoneria, musica, sistema o l'avviso.

2.  Frequenza &ndash; la frequenza di campionamento, espressa in Hz.

3.  Configurazione del canale &ndash; Mono o stereo.

4.  Formato audio &ndash; a 8 bit o la codifica a 16 bit.

5.  La dimensione del buffer &ndash; in byte.

6.  Modalità di buffer &ndash; streaming o statico.


Dopo la costruzione, il [riprodurre](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) metodo `AudioTrack` viene richiamato, per impostarlo in modo che avvia la riproduzione. Scrittura di buffer audio per la `AudioTrack` avvia la riproduzione:

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


#### <a name="pausing-and-stopping-the-playback"></a>Sospensione e l'interruzione della riproduzione

Chiamare il [sospendere](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) metodo per sospendere la riproduzione:

```csharp
audioTrack.Pause();
```

La chiamata di [arrestare](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) metodo terminerà la riproduzione in modo permanente:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Pulizia

Quando il `AudioTrack` è più necessario, le relative risorse devono essere liberate chiamando [versione](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La classe AudioRecord

Il [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) classe è l'equivalente di `AudioTrack` sul lato di registrazione. Ad esempio `AudioTrack`, Usa direttamente, i buffer di memoria al posto di file e gli URI. È necessario che il `RECORD_AUDIO` da set di autorizzazioni nel manifesto.


#### <a name="initializing-and-recording"></a>L'inizializzazione e la registrazione

Il primo passaggio consiste nel creare un nuovo [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) oggetto. L'elenco di argomenti passato il [costruttore](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) fornisce tutte le informazioni necessarie per la registrazione. A differenza di `AudioTrack`, in cui gli argomenti sono in gran parte enumerazioni, gli argomenti equivalenti in `AudioRecord` sono numeri interi. Sono inclusi:

1.  Hardware audio origine di input, ad esempio microfono.

2.  Tipo di flusso &ndash; vocale, suoneria, musica, sistema o l'avviso.

3.  Frequenza &ndash; la frequenza di campionamento, espressa in Hz.

4.  Configurazione del canale &ndash; Mono o stereo.

5.  Formato audio &ndash; a 8 bit o la codifica a 16 bit.

6.  Dimensioni in byte


Una volta il `AudioRecord` viene costruito, il relativo [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) metodo viene richiamato. È ora pronto per iniziare la registrazione. Il `AudioRecord` continuamente legge il buffer per l'input audio e scrive questo input out in un file audio.

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


#### <a name="stopping-the-recording"></a>L'arresto della registrazione

La chiamata di [arrestare](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) metodo termina la registrazione:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Pulizia

Quando il `AudioRecord` oggetto non è più necessario, chiamare il relativo [versione](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) metodo rilascia tutte le risorse associate:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Riepilogo

Il sistema operativo Android fornisce un framework potente per la riproduzione, registrazione e la gestione di audio. In questo articolo trattati come riprodurre e registrare l'audio usando di alto livello `MediaPlayer` e `MediaRecorder` classi. Successivamente, un esame di come usare le notifiche di audio per condividere le risorse audio del dispositivo tra applicazioni diverse. Infine, trattata come per la riproduzione e registrare l'audio utilizzando le API di basso livello, interfaccia direttamente con buffer di memoria.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo con Audio (esempio)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Registrazione di supporto](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Audio Manager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Traccia audio](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Audio Recorder](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
