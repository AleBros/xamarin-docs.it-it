---
title: Audio Android
description: Il sistema operativo Android offre ampio supporto per funzionalità multimediali, che comprende sia audio e video. Questa guida è incentrata sul audio in Android e la riproduzione e la registrazione tramite il lettore audio predefinito e le classi di registrazione, nonché l'API di basso livello audio dell'audio. Utilizzo degli eventi Audio broadcast da altre applicazioni, vengono inoltre descritti in modo che gli sviluppatori possono compilare applicazioni ben progettate.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 9b7e9354250881074fc6f0db5d97dc83e4d3fa77
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114665"
---
# <a name="android-audio"></a>Audio Android

_Il sistema operativo Android offre ampio supporto per funzionalità multimediali, che comprende sia audio e video. Questa guida è incentrata sul audio in Android e la riproduzione e la registrazione tramite il lettore audio predefinito e le classi di registrazione, nonché l'API di basso livello audio dell'audio. Utilizzo degli eventi Audio broadcast da altre applicazioni, vengono inoltre descritti in modo che gli sviluppatori possono compilare applicazioni ben progettate._


## <a name="overview"></a>Panoramica

I dispositivi mobili moderni hanno adottato una funzionalità che precedentemente hanno comportato dedicati parti di un'apparecchiatura &ndash; fotocamere, lettori di musica e video registrazioni. Per questo motivo, i framework multimediali sono diventati una funzionalità di alto livello per dispositivi mobili per le API.

Android offre ampio supporto per funzionalità multimediali. Questo articolo esamina l'utilizzo di audio in Android e include gli argomenti seguenti

1.  **Riproduzione di Audio con MediaPlayer** &ndash; usando l'oggetto incorporato `MediaPlayer` classe per la riproduzione di audio, inclusi file audio locali e con flusso audio con il `AudioTrack` classe.

2.  **La registrazione dell'Audio** &ndash; usando l'elemento predefinito `MediaRecorder` classe di registrare l'audio.

3.  **Lavorare con le notifiche Audio** &ndash; tramite le notifiche audio per creare applicazioni ben progettate che rispondono correttamente a eventi (ad esempio telefonate in arrivo) sospendendolo o annullamento relativi output audio.

4.  **Utilizzo di basso livello Audio** &ndash; riproduzione audio usando la `AudioTrack` classe scrivendo direttamente al buffer di memoria. La registrazione audio usando la `AudioRecord` classe e la lettura direttamente dai buffer della memoria.


## <a name="requirements"></a>Requisiti

Questa guida richiede Android 2.0 (API livello 5) o versione successiva. Si noti che il debug di audio in Android deve essere eseguito in un dispositivo.

È necessario alla richiesta il `RECORD_AUDIO` le autorizzazioni in **androidmanifest. XML**:

![Sezione autorizzazioni del manifesto Android con RECORD necessari\_AUDIO abilitata](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Riproduzione di Audio con la classe MediaPlayer

È il modo più semplice per riprodurre l'audio in Android con le funzionalità integrate [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) classe.
`MediaPlayer` possibile riprodurre i file locali o remoti, passando il percorso del file. Tuttavia, `MediaPlayer` è molto sensibile alla stato e la chiamata a uno dei relativi metodi nello stato errato genererà un'eccezione generata. È importante interagire con `MediaPlayer` nell'ordine descritto di seguito per evitare errori.



### <a name="initializing-and-playing"></a>L'inizializzazione e la riproduzione

Riproduzione di file audio con `MediaPlayer` richiede la sequenza seguente:

1. Creare un'istanza di una nuova [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) oggetto.

1. Configurare il file di riproduzione tramite la [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) (metodo).

1. Chiamare il [Prepare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) metodo per inizializzare il lettore.

1. Chiamare il [avviare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/) metodo per avviare la riproduzione di audio.


Esempio di codice seguente viene illustrato questo tipo di utilizzo:

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


### <a name="suspending-and-resuming-playback"></a>Sospensione e ripresa di riproduzione

La riproduzione di poter essere sospeso chiamando il [Sospendi](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) metodo:

```csharp
player.Pause();
```

Per riprendere la riproduzione sospesa, chiamare il [avviare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) (metodo).
Questa attività riprenderà dal percorso in pausa la riproduzione:

```csharp
player.Start();
```

Chiama il [arrestare](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) metodo sul lettore termina una riproduzione in corso:

```csharp
player.Stop();
```

Quando il lettore non è più necessario, le risorse devono essere rilasciate chiamando il [rilascio](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) metodo:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Utilizzo della classe MediaRecorder Record audio

Il corollario `MediaPlayer` per registrare l'audio in Android è il [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) classe. Ad esempio il `MediaPlayer`, è stato minuscole ed esegue la transizione attraverso diversi stati per arrivare al punto in cui è possibile avviare la registrazione. Per registrare l'audio, di `RECORD_AUDIO` autorizzazione deve essere impostata. Per istruzioni su come impostare l'applicazione autorizzazioni, vedere [funziona con file androidmanifest. XML](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>L'inizializzazione e la registrazione

La registrazione dell'audio con il `MediaRecorder` sono necessari i passaggi seguenti:

1. Creare un'istanza di una nuova [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) oggetto.

2. Specificare quale dispositivo hardware da usare per acquisire l'input audio tramite il [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) (metodo).

3. Impostare l'output di file di formato audio usando la [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) (metodo). Per un elenco dei tipi audio supportati, vedere [Android formati multimediali supportati](http://developer.android.com/guide/appendix/media-formats.html).

4. Chiamare il [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) metodo per impostare il tipo di codifica audio.

5. Chiamare il [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) metodo per specificare il nome del file di output scritti i dati audio.

6. Chiamare il [Prepare](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) metodo per inizializzare la registrazione.

7. Chiamare il [avviare](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) metodo per avviare la registrazione.


Esempio di codice seguente viene illustrata questa sequenza:

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


### <a name="stopping-recording"></a>Interruzione della registrazione

Per arrestare la registrazione, chiamare il `Stop` metodo di `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Pulizia

Una volta il `MediaRecorder` è stato arrestato, chiamare il [reimpostare](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) allo stato di metodo lo inserisce nello stato di inattività:

```csharp
recorder.Reset();
```

Quando il `MediaRecorder` è sono più necessari, le relative risorse devono essere liberate chiamando il [rilascio](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) metodo:

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>La gestione delle notifiche Audio



### <a name="the-audiomanager-class"></a>La classe AudioManager

Il [AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) classe fornisce l'accesso alle notifiche audio che consentono alle applicazioni di sapere quando si verificano eventi audio. Questo servizio fornisce inoltre l'accesso ad altre funzionalità audio, ad esempio il volume e suoneria controllo modalità. Il `AudioManager` consente a un'applicazione gestire le notifiche audio per controllare la riproduzione audio.



### <a name="managing-audio-focus"></a>Gestione degli elementi attivi Audio

Le risorse audio del dispositivo (incorporato Windows Media player e traccia eventi) sono condivise da tutte le applicazioni in esecuzione.

Concettualmente, come avviene per le applicazioni in un computer desktop in cui solo un'applicazione ha lo stato attivo della tastiera: dopo aver selezionato una delle applicazioni in esecuzione facendo clic del mouse, l'input della tastiera passa solo a tale applicazione.

Lo stato attivo audio è un concetto simile e impedisce a più di un'applicazione la riproduzione e la registrazione dell'audio nello stesso momento. È stato attivo della tastiera più complicato perché è volontaria &ndash; l'applicazione può ignorare il fatto che non è attualmente ha lo stato attivo audio e riprodurre indipendentemente dal fatto che &ndash; e perché esistono diversi tipi di messa a fuoco audio che può essere richiesta. Ad esempio, se il richiedente è previsto solo per riprodurre l'audio per un tempo molto breve, può chiedere temporaneo messa a fuoco.

Audio messa a fuoco può essere soddisfatta immediatamente, o inizialmente negato e concesse in un secondo momento. Ad esempio, se un'applicazione richieste audio messa a fuoco durante una chiamata telefonica, verranno rifiutate, ma lo stato attivo può essere concessa anche al termine della chiamata telefonica. In questo caso, un listener è registrato per poter rispondere di conseguenza se lo stato attivo audio non viene eseguito da subito. La richiesta dello stato attivo audio viene utilizzata per determinare se è sufficiente riprodurre o registrare l'audio.

Per altre informazioni sullo stato attivo audio, vedere [la gestione dello stato di attivo Audio](http://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>La registrazione di Callback per lo stato attivo Audio

La registrazione di `FocusChangeListener` callback dal `IOnAudioChangeListener` è una parte importante di ottenere e rilasciare audio messa a fuoco. Questo avviene perché la concessione dell'audio messa a fuoco può essere rinviata fino a un secondo momento. Ad esempio, un'applicazione può richiedere da riprodurre musica mentre è in corso una chiamata telefonica. Lo stato attivo audio non verrà concessa fino al termine della chiamata telefonica.

Per questo motivo, l'oggetto di callback viene passato come parametro nel `GetAudioFocus` metodo di `AudioManager`, ed è questa chiamata che esegue la registrazione del callback. Se lo stato attivo audio è inizialmente negato ma in un secondo momento concesso, l'applicazione è stata informata richiamando `OnAudioFocusChange` nel callback. Il metodo di stesso viene utilizzato per indicare all'applicazione che lo stato attivo audio verrà portato da subito.

Quando l'applicazione ha terminato di utilizzare le risorse di audio, chiama il `AbandonFocus` metodo di `AudioManager`e lo passa nuovamente nel callback. Ciò Annulla la registrazione del callback e rilascia le risorse di audio, in modo che altre applicazioni possono ottenere lo stato attivo audio.



#### <a name="requesting-audio-focus"></a>La richiesta Audio messa a fuoco

I passaggi necessari per richiedere le risorse audio del dispositivo sono i seguenti:

1.  Ottenere un handle per il `AudioManager` il servizio di sistema.

2.  Creare un'istanza della classe di callback.

3.  Richiedere le risorse audio del dispositivo chiamando il `RequestAudioFocus` metodo su di `AudioManager` . I parametri sono l'oggetto callback, il tipo di flusso (musica, chiamata vocale, ad anello e così via) e il tipo dell'accesso al diritto richiesto (audio risorse possono essere richiesta momentaneamente o per un periodo di tempo indefinito, ad esempio).

4.  Se la richiesta viene concesso, il `playMusic` metodo viene richiamato immediatamente e avvia la riproduzione audio.

5.  Se la richiesta viene negata, viene eseguita alcuna azione ulteriore. In questo caso, l'audio verrà riprodotto solo se la richiesta viene concessa in un secondo momento.


L'esempio di codice seguente illustra questi passaggi:

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


#### <a name="releasing-audio-focus"></a>Il rilascio di Audio messa a fuoco

Quando la riproduzione della traccia è stata completata, il `AbandonFocus` metodo `AudioManager` viene richiamato. In questo modo un'altra applicazione ottenere le risorse audio del dispositivo. Altre applicazioni riceveranno una notifica della modifica dello stato attivo audio se ha registrato i propri listener.


## <a name="low-level-audio-api"></a>API Audio a basso livello

Le API audio a basso livello forniscono un maggiore controllo sulla riproduzione di audio e la registrazione perché interagiscono direttamente con il buffer di memoria invece di usare gli URI di file. Esistono alcuni scenari in cui questo approccio è preferibile. Tali scenari includono:

1.  Quando si riproduce da file audio crittografati.

2.  Quando si riproduce una successione di brevi clip.

3.  Per lo streaming di audio.


### <a name="audiotrack-class"></a>Classe AudioTrack

Il [AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) classe Usa le API audio a basso livello per la registrazione ed è l'equivalente di basso livello di `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>L'inizializzazione e la riproduzione

Per la riproduzione di audio, una nuova istanza della `AudioTrack` deve essere creata un'istanza. L'elenco di argomenti passati i [costruttore](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) specifica come riprodurre il campione audio contenuto nel buffer. Gli argomenti sono:

1.  Stream tipo &ndash; vocali, suoneria, musica, sistema o l'allarme.

2.  Frequenza &ndash; la frequenza di campionamento espressa in Hz.

3.  Configurazione del canale &ndash; Mono o stereo.

4.  Formato audio &ndash; a 8 bit o la codifica a 16 bit.

5.  Le dimensioni del buffer &ndash; in byte.

6.  Modalità di buffer &ndash; streaming o statico.


Dopo la costruzione, i [riprodurre](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) metodo `AudioTrack` viene richiamato per impostarlo per la riproduzione di inizio. La scrittura di buffer di audio per il `AudioTrack` avvia la riproduzione:

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

Chiamare il [sospendere](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) metodo sospendere la riproduzione:

```csharp
audioTrack.Pause();
```

Chiama il [arrestare](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) metodo verrà interrotta la riproduzione in modo permanente:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Pulizia

Quando la `AudioTrack` è più necessario, le relative risorse devono essere liberate chiamando [versione](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La classe AudioRecord

Il [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) classe è l'equivalente di `AudioTrack` sul lato di registrazione. Ad esempio `AudioTrack`, Usa direttamente, buffer di memoria al posto di file e gli URI. Si presuppone che il `RECORD_AUDIO` impostare l'autorizzazione nel manifesto.


#### <a name="initializing-and-recording"></a>L'inizializzazione e la registrazione

Il primo passaggio consiste nel creare una nuova [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) oggetto. L'elenco di argomenti passati i [costruttore](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) fornisce tutte le informazioni necessarie per la registrazione. A differenza di `AudioTrack`, in cui gli argomenti sono in gran parte le enumerazioni, gli argomenti equivalenti in `AudioRecord` sono numeri interi. Sono inclusi:

1.  Hardware origine di input audio, ad esempio microfono.

2.  Stream tipo &ndash; vocali, suoneria, musica, sistema o l'allarme.

3.  Frequenza &ndash; la frequenza di campionamento espressa in Hz.

4.  Configurazione del canale &ndash; Mono o stereo.

5.  Formato audio &ndash; a 8 bit o la codifica a 16 bit.

6.  Dimensioni in byte


Una volta il `AudioRecord` viene costruito, relativi [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) metodo viene richiamato. È ora pronto per iniziare la registrazione. Il `AudioRecord` continuamente legge il buffer di input audio e scrive questo input out in un file audio.

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

Chiama il [arrestare](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) metodo termina la registrazione:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Pulizia

Quando la `AudioRecord` oggetto non è più necessario, la chiamata a relativo [rilascio](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) metodo rilascia tutte le risorse associate:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Riepilogo

Il sistema operativo Android fornisce un framework potente per la riproduzione, la registrazione e la gestione di audio. Questo articolo ha illustrato come riprodurre e registrare audio senza di alto livello `MediaPlayer` e `MediaRecorder` classi. Successivamente, è stato spiegato come usare le notifiche audio per condividere le risorse audio del dispositivo tra applicazioni diverse. Infine, trattata come per la riproduzione e registrare l'audio usando le API di basso livello, quali interfacciarsi direttamente con buffer di memoria.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo con Audio (esempio)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Registrazione supporti](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Gestione audio](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Traccia audio](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Registrazione audio](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
