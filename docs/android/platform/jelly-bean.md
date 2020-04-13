---
title: Jelly Bean Caratteristiche
description: 'Questo documento fornirà una panoramica di alto livello delle nuove funzionalità per gli sviluppatori introdotte in Android 4.1. Queste funzionalità includono: notifiche avanzate, aggiornamenti ad Android Beam per condividere file di grandi dimensioni, aggiornamenti per multimedia, individuazione di rete peer-to-peer, animazioni, nuove autorizzazioni.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a638ccf7810c737faaeded7fcc98fcf657c85288
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027203"
---
# <a name="jelly-bean-features"></a>Jelly Bean Caratteristiche

_Questo documento fornirà una panoramica di alto livello delle nuove funzionalità per gli sviluppatori introdotte in Android 4.1. Queste funzionalità includono: notifiche avanzate, aggiornamenti ad Android Beam per condividere file di grandi dimensioni, aggiornamenti per multimedia, individuazione di rete peer-to-peer, animazioni, nuove autorizzazioni._

## <a name="overview"></a>Panoramica

Android 4.1 (API Level 16), noto anche come "Jelly Bean", è stato rilasciato il 9 luglio 2012. Questo articolo fornirà un'introduzione di alto livello ad alcune delle nuove funzionalità di Android 4.1 per gli sviluppatori che utilizzano Xamarin.Android. Alcune di queste nuove funzionalità introdotte sono miglioramenti alle animazioni per l'avvio di un'attività, nuovi suoni per una fotocamera e un supporto migliorato per la navigazione dello stack dell'applicazione. Ora è possibile tagliare e incollare con finalità.

La stabilità delle applicazioni Android è migliorata con la possibilità di isolare la dipendenza dai provider di contenuti instabili. I servizi possono anche essere isolati in modo che siano accessibili solo dall'attività che li ha avviati.

È stato aggiunto il supporto per l'individuazione dei servizi di rete tramite Bonjour, UPnP o servizi basati su DNS multicast. È ora possibile per le notifiche più ricche che hanno formattato il testo, pulsanti di azione e immagini di grandi dimensioni.

Infine diverse nuove autorizzazioni sono state aggiunte in Android 4.1.

## <a name="requirements"></a>Requisiti

Per sviluppare applicazioni Xamarin.Android utilizzando Jelly Bean richiede Xamarin.Android 4.2.6 o superiore e Android 4.1 (livello API 16) essere installato tramite Android SDK Manager, come illustrato nella schermata seguente:

[![Selezione di Android 4.1 in Gestione SDK Android](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>Novità

### <a name="animations"></a>Animations

Le attività possono essere avviate usando animazioni di zoom o animazioni personalizzate usando la `ActivityOptions` classe . I seguenti nuovi metodi vengono forniti per supportare queste animazioni:The following new methods are provided to support these animations:

- `MakeScaleUpAnimation`– Verrà creata un'animazione che consente di aumentare la scala di una finestra attività da una posizione iniziale e le dimensioni sullo schermo.
- `MakeThumbnailScaleUpAnimation`– Questo creerà un'animazione che scala da un'immagine di anteprima dalla posizione specificata sullo schermo.
- `MakeCustomAnimation`– Questo crea un'animazione dalle risorse nell'applicazione. C'è un'animazione per quando l'attività si apre e un'altra per quando l'attività si interrompe.

La `TimeAnimator` nuova classe `TimeAnimator.ITimeListener` fornisce un'interfaccia che può notificare un'applicazione ogni volta che un frame viene modificato in un'animazione. Si consideri, ad `TimeAnimator.ITimeListener`esempio, la seguente implementazione di :

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E ora per usare la `TimeAnimator` classe, viene creata un'istanza di e il listener è impostato:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Mentre `TimeAnimator` l'istanza è in `ITimeAnimator.ITimeListener`esecuzione, verrà richiamata , che registrerà per quanto tempo l'animatore è stato eseguito e per quanto tempo è stato dall'ultima volta che il metodo è stato richiamato.

### <a name="application-stack-navigation"></a>Navigazione dello stack di applicazioni

Android 4.1 migliora la navigazione dello stack di applicazioni che è stato introdotto in Android 3.0. `ParentName` Specificando la proprietà di `ActivityAttribute`, Android può aprire l'attività padre appropriata quando l'utente preme il [pulsante Su](https://developer.android.com/design/patterns/navigation.html#up-vs-back) sulla barra delle azioni - Android creerà un'istanza dell'attività `ParentName` specificata dalla proprietà . Ciò consente alle applicazioni di mantenere la gerarchia delle attività che eseguono una determinata attività.

Per la maggior `ParentName` parte delle applicazioni l'impostazione di on the activity è sufficiente informazioni per Android per fornire il comportamento corretto per lo spostamento nello stack dell'applicazione; Android sintetizza lo stack indietro necessario creando una serie di finalità per ogni attività padre. Tuttavia, poiché si tratta di uno stack di applicazione artificiale, ogni attività sintetica non avrà lo stato salvato che avrebbe un'attività naturale. Per fornire lo stato salvato a un'attività `OnPrepareNavigationUpTaskStack` padre sintetica, un'attività può eseguire l'override del metodo. Questo metodo riceve `TaskStackBuilder` un'istanza che avrà una raccolta di Intent oggetti che Android utilizzerà per creare lo stack indietro. L'attività può modificare queste finalità in modo che, quando viene creata l'attività sintetica, riceverà le informazioni sullo stato appropriate.

Per scenari più complessi, sono disponibili nuovi metodi nella classe Activity che possono essere utilizzati per gestire il comportamento dell'esplorazione Up e costruire lo stack Indietro:For more complex scenarios, there are new methods on the Activity class that may be used to handle the behavior of Up navigation and construct the back stack:

- `OnNavigateUp`– Eseguendo l'override di questo metodo è possibile eseguire un'azione personalizzata quando viene premuto il pulsante **Su.**
- `NavigateUpTo`– La chiamata a questo metodo causerà l'applicazione per passare dall'attività corrente all'attività specificata da una determinata finalità.
- `ParentActivityIntent`– Viene utilizzato per ottenere un intento che avvierà l'attività padre dell'attività corrente.
- `ShouldUpRecreateTask`: questo metodo viene utilizzato per eseguire una query se è necessario creare lo stack indietro sintetico per passare a un'attività padre. Restituisce `true` se è necessario creare lo stack sintetico. 
- `FinishAffinity`– La chiamata a questo metodo terminerà l'attività corrente e tutte le attività sottostanti nell'attività corrente che hanno la stessa affinità di attività.
- `OnCreateNavigateUpTaskStack`– Questo metodo viene sottoposto a override quando è necessario avere il controllo completo su come viene creato lo stack sintetico.

### <a name="camera"></a>Camera

Esiste una nuova `Camera.IAutoFocusMoveCallback`interfaccia, che può essere utilizzata per rilevare quando lo stato attivo automatico è stato avviato o interrotto. Un esempio di questa nuova interfaccia può essere visualizzato nel frammento di codice seguente:An example of this new interface can be seen in the following snippet:

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

La nuova `MediaActionSound` classe fornisce un set di API per la produzione di suoni per le varie azioni multimediali. Ci sono diverse azioni che possono verificarsi con una `Android.Media.MediaActionSoundType`fotocamera, queste sono definite dall'enumerazione :

- `MediaActionSoundType.FocusComplete`– Questo suono che viene riprodotto quando la messa a fuoco è stata completata.
- `MediaActionSoundType.ShutterClick`– Questo suono viene riprodotto quando viene scattata una foto dell'immagine fissa.
- `MediaActionSoundType.StartVideoRecording`– Questo suono viene utilizzato per indicare l'inizio della registrazione video.
- `MediaActionSoundType.StopVideoRecording`– Questo suono verrà riprodotto per indicare la fine della registrazione video.

Un esempio di come `MediaActionSound` usare la classe può essere visualizzato nel frammento di codice seguente:An example of how to use the class can be seen in the following snippet:

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```

### <a name="connectivity"></a>Connettività

#### <a name="android-beam"></a>Android Beam

Android Beam è una tecnologia basata su NFC che consente a due dispositivi Android di comunicare tra loro. Android 4.1 fornisce un supporto migliore per il trasferimento di file di grandi dimensioni. Quando si utilizza `NfcAdapter.SetBeamPushUris()` il nuovo metodo Android passerà tra meccanismi di trasporto alternativi (come Bluetooth) per raggiungere una velocità di trasferimento veloce.

#### <a name="network-services-discovery"></a>Individuazione servizi di rete

Android 4.1 contiene nuove API per l'individuazione dei servizi multicast basati su DNS.
Ciò consente a un'applicazione di rilevare e connettersi tramite Wi-Fi ad altri dispositivi, ad esempio stampanti, fotocamere e dispositivi multimediali. Queste nuove API sono `Android.Net.Nsd` nel pacchetto.

Per creare un servizio che può essere `NsdServiceInfo` utilizzato da altri servizi, la classe viene utilizzata per creare un oggetto che definirà le proprietà di un servizio. Questo oggetto viene `NsdManager.RegisterService()` quindi fornito a `NsdManager.ResolveListener`insieme a un'implementazione di . Le implementazioni di `NsdManager.ResolveListener` vengono utilizzate per notificare la corretta registrazione e per annullare la registrazione del servizio.

Per individuare i servizi in `Nsd.DiscoveryListener` rete `NsdManager.discoverServices()`e l'implementazione di passato a .

#### <a name="network-usage"></a>Utilizzo rete

Un nuovo `ConnectivityManager.IsActiveNetworkMetered` metodo consente a un dispositivo di verificare se è connesso a una rete a consumo. Questo metodo può essere utilizzato per gestire l'utilizzo dei dati informando accuratamente gli utenti che potrebbero essere previsti costi costosi per le operazioni sui dati.

#### <a name="wifi-direct-service-discovery"></a>Individuazione diretta dei servizi WiFi

La `WifiP2pManager` classe è stata introdotta in Android 4.0 per supportare *zeroconf*. La rete zeroconf (zero configuration networking) è un insieme di tecniche che consente ai dispositivi (computer, stampanti, telefoni) di connettersi automaticamente alle reti, con l'intervento di operatori di rete umana o server di configurazione speciali.

In Jelly Bean, `WifiP2pManager` può scoprire dispositivi nelle vicinanze utilizzando *Bonjour* o *Upnp*. Bonjour è l'implementazione di Apple di zeroconf. Upnp è un insieme di protocolli di rete che supporta anche zeroconf. I seguenti metodi `WiFiP2pManager` aggiunti al per supportare l'individuazione del servizio Wi-Fi:

- `AddLocalService()`– Questo metodo viene utilizzato annunciare un'applicazione come servizio tramite Wi-Fi per l'individuazione da parte dei peer.
- `AddServiceRequest(`) – Questo metodo consiste nell'inviare una richiesta di individuazione del servizio al framework. Viene utilizzato per inizializzare l'individuazione del servizio Wi-Fi.
- `SetDnsSdResponseListeners()`– Questo metodo viene utilizzato per registrare i callback da richiamare alla ricezione di una risposta alle richieste di individuazione da Bonjour.
- `SetUpnpServiceResponseListener()`– Questo metodo viene utilizzato per registrare i callback da richiamare alla ricezione di una risposta alle richieste di individuazione Upnp.

### <a name="content-providers"></a>Provider di contenuto

La `ContentResolver` classe ha ricevuto `AcquireUnstableContentProvider`un nuovo metodo, . Questo metodo consente a un'applicazione di acquisire un provider di contenuti "instabile". In genere, quando un'applicazione acquisisce un provider di contenuti e tale provider si arresta in modo anomalo, anche l'applicazione. Con questa chiamata al metodo, un'applicazione non si arresta in modo anomalo se il provider di contenuti si arresta in modo anomalo. Al `Android.OS.DeadObjectionException` contrario, verrà generata dalle chiamate sul provider di contenuti per informare un'applicazione che il provider di contenuti è andato via. Un provider di contenuti "instabile" è utile quando si interagisce con i provider di contenuti da altre applicazioni: è meno probabile che il codice buggy di un'altra applicazione influisca su un'altra applicazione.

### <a name="copy-and-paste-with-intents"></a>Copia e incolla con finalità

La `Intent` classe può `ClipData` ora avere un `Intent.ClipData` oggetto associato tramite la proprietà . Questo metodo consente di trasmettere dati aggiuntivi dagli Appunti con l'intento. Un'istanza `ClipData` di può `ClipData.Item`contenere uno o più file . `ClipData.Item`sono elementi dei seguenti tipi:

- **Testo** – Si tratta di qualsiasi stringa di testo, HTML o qualsiasi stringa il cui formato è supportato dallo stile Android incorporato si estende.
- **Intento** `Intent` – Qualsiasi oggetto.
- **Uri:** può trattarsi di qualsiasi URI, ad esempio un segnalibro HTTP o l'URI di un provider di contenuti.

### <a name="isolated-services"></a>Servizi isolati

Un servizio isolato è un servizio che viene eseguito con il proprio processo speciale e non dispone di autorizzazioni proprie. L'unica comunicazione con il servizio è quando si avvia il servizio e l'associazione a esso tramite l'API del servizio. È possibile dichiarare un servizio come isolato impostando la proprietà `IsolatedProcess="true"` in `ServiceAttribute` che adorna una classe di servizio.

### <a name="media"></a>Contenuti multimediali

La `Android.Media.MediaCodec` nuova classe fornisce un'API ai codec multimediali di basso livello. Le applicazioni possono interrogare il sistema per scoprire quali codec di basso livello sono disponibili sul dispositivo.

Le `Android.Media.Audiofx.AudioEffect` nuove sottoclassi sono state aggiunte per supportare la pre-elaborazione audio aggiuntiva sull'audio acquisito:

- `Android.Media.Audiofx.AcousticEchoCanceler`– Questa classe viene utilizzata per la pre-elaborazione audio per rimuovere il segnale da una parte remota da un segnale audio acquisito. Ad esempio, la rimozione dell'eco da un'applicazione di comunicazione vocale.
- `Android.Media.Audiofx.AutomaticGainControl`– Questa classe viene utilizzata per normalizzare il segnale acquisito aumentando o abbassando un segnale di ingresso in modo che il segnale di uscita sia costante.
- `Android.Media.Audiofx.NoiseSuppressor`– Questa classe rimuoverà il rumore di fondo dal segnale acquisito.

Non tutti i dispositivi supporteranno questi effetti. Il `AudioEffect.IsAvailable` metodo deve essere chiamato da un'applicazione per verificare se l'effetto audio in questione è supportato nel dispositivo che esegue l'applicazione.

La `MediaPlayer` classe supporta ora la `SetNextMediaPlayer()` riproduzione senza spazi con il metodo . Questo nuovo metodo specifica il MediaPlayer successivo da avviare al termine della riproduzione del lettore multimediale corrente.

Le nuove classi seguenti forniscono meccanismi standard e l'interfaccia utente per la selezione dei contenuti multimediali da riprodurre:

- `MediaRouter`– Questa classe consente alle applicazioni di controllare il routing dei canali multimediali da un dispositivo a altoparlanti esterni o altri dispositivi.
- `MediaRouterActionProvider`e `MediaRouteButton` – Queste classi aiutano a fornire un'interfaccia utente coerente per la selezione e la riproduzione di contenuti multimediali.

### <a name="notifications"></a>Notifiche

Android 4.1 consente alle applicazioni una maggiore flessibilità e controllo con la visualizzazione delle notifiche. Le applicazioni possono ora mostrare notifiche più grandi e migliori agli utenti. Un nuovo `NotificationBuilder.SetStyle()` metodo, consente di impostare uno dei tre nuovi stili nelle notifiche:

- `Notification.BigPictureStyle`– Si tratta di una classe helper che genererà notifiche che avranno un'immagine in essi. L'immagine seguente mostra un esempio di notifica con un'immagine grande:The following image shows an example of a notification with a big image:

 [![Schermata di esempio di una notifica BigPictureStyleExample screenshot of a BigPictureStyle notification](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`– Si tratta di una classe helper che genererà notifiche che avranno più righe di testo, ad esempio e-mail. Un esempio di questo nuovo stile di notifica può essere visualizzato nella schermata seguente:An example of this new notification style can be seen in the following screenshot:

 [![Schermata di esempio di una notifica BigTextStyleExample screenshot of a BigTextStyle notification](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`– Si tratta di una classe helper che genererà notifiche che contengono un elenco di stringhe, ad esempio frammenti da un messaggio di posta elettronica, come illustrato in questa schermata:– This is a helper class that will generate notifications that contain a list of strings, such as snippets from an e-mail message, as shown in this screenshot:

 [![Schermata di esempio di una notifica Notification.InboxStyleExample screenshot of a Notification.InboxStyle notification](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

È possibile aggiungere fino a due pulsanti di azione nella parte inferiore di un messaggio di notifica quando la notifica utilizza lo stile normale o più grande.
Un esempio di questo può essere visto nella schermata seguente, in cui i pulsanti di azione sono visibili nella parte inferiore della notifica:

 [![Schermata di esempio dei pulsanti di azione visualizzati sotto un messaggio di notifica](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La `Notification` classe ha ricevuto nuove costanti che consentono a uno sviluppatore di specificare uno dei cinque livelli di priorità per una notifica. Questi possono essere impostati `Priority` su una notifica utilizzando la proprietà .

### <a name="permissions"></a>Autorizzazioni

Sono state aggiunte le seguenti nuove autorizzazioni:

- `READ_EXTERNAL_STORAGE`- L'applicazione richiede l'accesso in sola lettura all'archiviazione esterna.- The application requires read only access to external storage. Attualmente tutte le applicazioni dispongono dell'accesso in lettura per impostazione predefinita, ma le versioni future di Android richiederanno esplicitamente l'accesso in lettura da parte delle applicazioni.
- `READ_USER_DICTIONARY`- Consente un accesso in lettura al dizionario delle parole dell'utente.- Allows a read-access to the user's word dictionary.
- `READ_CALL_LOG`- Consente a un'applicazione di ottenere informazioni sulle chiamate in entrata e in uscita leggendo il registro chiamate.
- `WRITE_CALL_LOG`- Consente a un'applicazione di scrivere nel registro chiamate sul telefono.
- `WRITE_USER_DICTIONARY`- Consente a un'applicazione di scrivere nel dizionario delle parole dell'utente.- Allows an application to write to the user's word dictionary.

Un cambiamento `READ_EXTERNAL_STORAGE` importante da notare - attualmente questa autorizzazione viene concessa automaticamente da Android. Le versioni future di Android richiederanno un'applicazione per richiedere questa autorizzazione prima di concedere l'autorizzazione.

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto alcune delle nuove API disponibili in Android 4.1 (livello API 16). Ha evidenziato alcune modifiche per le animazioni e l'animazione del lancio di un'attività e ha introdotto le nuove API per l'individuazione di rete di altri dispositivi utilizzando protocolli come Bonjour o UPnP. Sono state evidenziate anche altre modifiche all'API, come la possibilità di tagliare e incollare i dati tramite finalità, la possibilità di utilizzare servizi isolati o provider di contenuti "instabili".

Questo articolo ha poi continuato a introdurre gli aggiornamenti alle notifiche, e discusso alcune delle nuove autorizzazioni che sono state introdotte con Android 4.1

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione temporale (esempio)Time Animation Example (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [API di Android 4.1](https://developer.android.com/about/versions/android-4.1.html)
- [Attività e stack indietro](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navigazione con Backup e Su](https://developer.android.com/design/patterns/navigation.html)
