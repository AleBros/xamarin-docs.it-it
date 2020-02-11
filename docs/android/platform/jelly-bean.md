---
title: Funzionalità Jelly Bean
description: Questo documento fornisce una panoramica di alto livello delle nuove funzionalità per gli sviluppatori introdotte in Android 4,1. Queste funzionalità includono le notifiche avanzate, gli aggiornamenti al Beam Android per la condivisione di file di grandi dimensioni, gli aggiornamenti a Multimedia, l'individuazione di rete peer-to-peer, le animazioni e le nuove autorizzazioni.
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a638ccf7810c737faaeded7fcc98fcf657c85288
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027203"
---
# <a name="jelly-bean-features"></a>Funzionalità Jelly Bean

_Questo documento fornisce una panoramica di alto livello delle nuove funzionalità per gli sviluppatori introdotte in Android 4,1. Queste funzionalità includono le notifiche avanzate, gli aggiornamenti al Beam Android per la condivisione di file di grandi dimensioni, gli aggiornamenti a Multimedia, l'individuazione di rete peer-to-peer, le animazioni e le nuove autorizzazioni._

## <a name="overview"></a>Panoramica

Android 4,1 (livello API 16), noto anche come "Jelly Bean", è stato rilasciato il 9 luglio 2012. Questo articolo fornisce un'introduzione generale ad alcune delle nuove funzionalità di Android 4,1 per gli sviluppatori che usano Xamarin.Android. Alcune di queste nuove funzionalità introdotte sono i miglioramenti apportati alle animazioni per l'avvio di un'attività, nuovi suoni per una fotocamera e un supporto migliorato per la navigazione dello stack dell'applicazione. È ora possibile tagliare e incollare con Intent.

La stabilità delle applicazioni Android è stata migliorata con la possibilità di isolare la dipendenza da provider di contenuti instabili. I servizi possono anche essere isolati in modo che siano accessibili solo dall'attività che le ha avviate.

È stato aggiunto il supporto per l'individuazione di servizi di rete mediante i servizi Bonjour, UPnP o multicast basati su DNS. È ora possibile ottenere notifiche più complete con testo formattato, pulsanti di azione e immagini di grandi dimensioni.

Sono state aggiunte infine alcune nuove autorizzazioni in Android 4,1.

## <a name="requirements"></a>Requisiti

Per lo sviluppo di applicazioni Xamarin.Android con Jelly Bean è necessario che Xamarin.Android 4.2.6 o versione successiva e Android 4,1 (API level 16) venga installato tramite Android SDK Manager, come illustrato nella schermata seguente:

[![la selezione di Android 4,1 in gestione Android SDK](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>Novità

### <a name="animations"></a>Animations

Le attività possono essere avviate tramite animazioni zoom o animazioni personalizzate utilizzando la classe `ActivityOptions`. Per supportare queste animazioni, vengono forniti i nuovi metodi seguenti:

- `MakeScaleUpAnimation`: verrà creata un'animazione che aumenta le dimensioni di una finestra attività da una posizione iniziale e una dimensione sullo schermo.
- `MakeThumbnailScaleUpAnimation`: verrà creata un'animazione che aumenta da un'immagine di anteprima dalla posizione specificata sullo schermo.
- `MakeCustomAnimation`: crea un'animazione dalle risorse nell'applicazione. È presente un'animazione per l'apertura dell'attività e un'altra per l'arresto dell'attività.

La nuova classe `TimeAnimator` fornisce un'interfaccia `TimeAnimator.ITimeListener` che può notificare a un'applicazione ogni volta che un frame viene modificato in un'animazione. Si consideri, ad esempio, l'implementazione di `TimeAnimator.ITimeListener`seguente:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

Ora, per usare la classe, viene creata un'istanza di `TimeAnimator` e viene impostato il listener:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Quando l'istanza di `TimeAnimator` è in esecuzione, richiamerà `ITimeAnimator.ITimeListener`, che registrerà il tempo di esecuzione dell'animatore e il tempo trascorso dall'ultima volta in cui è stato richiamato il metodo.

### <a name="application-stack-navigation"></a>Navigazione dello stack dell'applicazione

Android 4,1 migliora la navigazione dello stack dell'applicazione introdotta in Android 3,0. Specificando la proprietà `ParentName` della `ActivityAttribute`, Android può aprire l'attività padre appropriata quando l'utente preme il [pulsante](https://developer.android.com/design/patterns/navigation.html#up-vs-back) su sulla barra delle azioni-Android creerà un'istanza dell'attività specificata dalla proprietà `ParentName`. Ciò consente alle applicazioni di mantenere la gerarchia di attività che rendono un'attività specifica.

Per la maggior parte delle applicazioni, l'impostazione della `ParentName` sull'attività è sufficiente per Android fornire il comportamento corretto per lo spostamento nello stack di applicazioni; Android sintetizza il back stack necessario creando una serie di Intent per ogni attività padre. Tuttavia, poiché si tratta di uno stack di applicazioni artificiale, ogni attività sintetica non avrà lo stato salvato di un'attività naturale. Per fornire lo stato salvato a un'attività padre sintetica, un'attività può eseguire l'override del metodo `OnPrepareNavigationUpTaskStack`. Questo metodo riceve un'istanza `TaskStackBuilder` che avrà una raccolta di oggetti Intent che Android userà per creare lo stack indietro. L'attività può modificare questi Intent in modo che, durante la creazione dell'attività sintetica, ricevano le informazioni di stato corrette.

Per scenari più complessi, sono disponibili nuovi metodi per la classe Activity che possono essere usati per gestire il comportamento di spostamento verso l'alto e costruire lo stack indietro:

- `OnNavigateUp`: se si esegue l'override di questo metodo, è possibile eseguire un'azione personalizzata quando si preme il pulsante **freccia su** .
- `NavigateUpTo`: la chiamata a questo metodo determinerà l'esplorazione dell'applicazione dall'attività corrente all'attività specificata da un determinato scopo.
- `ParentActivityIntent`: viene usato per ottenere un preventivo che avvierà l'attività padre dell'attività corrente.
- `ShouldUpRecreateTask`: questo metodo viene usato per eseguire una query se è necessario creare lo stack di backup sintetico per passare a un'attività padre. Restituisce `true` se è necessario creare lo stack sintetico. 
- `FinishAffinity`: la chiamata a questo metodo completerà l'attività corrente e tutte le attività sottostanti nell'attività corrente che hanno la stessa affinità di attività.
- `OnCreateNavigateUpTaskStack`: questo metodo viene sottoposto a override quando è necessario avere il controllo completo sulla modalità di creazione dello stack sintetico.

### <a name="camera"></a>Fotocamera

È disponibile una nuova interfaccia, `Camera.IAutoFocusMoveCallback`, che può essere usata per rilevare quando lo stato attivo è stato avviato o interrotto. Un esempio di questa nuova interfaccia può essere visualizzato nel frammento di codice seguente:

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

La nuova classe `MediaActionSound` fornisce un set di API per la creazione di suoni per le varie azioni multimediali. Con una fotocamera possono verificarsi diverse azioni che vengono definite dall'enumerazione `Android.Media.MediaActionSoundType`:

- `MediaActionSoundType.FocusComplete`: questo suono viene riprodotto al termine della messa a fuoco.
- `MediaActionSoundType.ShutterClick`: questo suono verrà riprodotto quando viene acquisita un'immagine di immagine ancora.
- `MediaActionSoundType.StartVideoRecording`: questo suono viene usato per indicare l'inizio della registrazione video.
- `MediaActionSoundType.StopVideoRecording`: questo suono verrà riprodotto per indicare la fine della registrazione video.

Un esempio di come usare la classe `MediaActionSound` può essere visualizzato nel frammento di codice seguente:

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

Android Beam è una tecnologia basata su NFC che consente a due dispositivi Android di comunicare tra loro. Android 4,1 fornisce un supporto migliore per il trasferimento di file di grandi dimensioni. Quando si usa il nuovo metodo `NfcAdapter.SetBeamPushUris()` Android passa tra meccanismi di trasporto alternativi, ad esempio Bluetooth, per ottenere una velocità di trasferimento rapida.

#### <a name="network-services-discovery"></a>Individuazione dei servizi di rete

Android 4,1 contiene nuove API per l'individuazione del servizio basato su DNS multicast.
Questo consente a un'applicazione di rilevare e connettersi tramite Wi-Fi ad altri dispositivi, ad esempio stampanti, fotocamere e dispositivi multimediali. Queste nuove API si trovano nel pacchetto di `Android.Net.Nsd`.

Per creare un servizio che può essere utilizzato da altri servizi, la classe `NsdServiceInfo` viene utilizzata per creare un oggetto che definirà le proprietà di un servizio. Questo oggetto viene quindi fornito per `NsdManager.RegisterService()` insieme a un'implementazione di `NsdManager.ResolveListener`. Le implementazioni di `NsdManager.ResolveListener` vengono utilizzate per notificare una corretta registrazione e per annullare la registrazione del servizio.

Per individuare i servizi sulla rete e l'implementazione di `Nsd.DiscoveryListener` passati a `NsdManager.discoverServices()`.

#### <a name="network-usage"></a>Utilizzo della rete

Un nuovo metodo, `ConnectivityManager.IsActiveNetworkMetered` consente a un dispositivo di verificare se è connesso a una rete a consumo. Questo metodo può essere utilizzato per semplificare la gestione dell'utilizzo dei dati, in modo da informare accuratamente gli utenti che potrebbero essere presenti costi costosi per le operazioni sui dati.

#### <a name="wifi-direct-service-discovery"></a>Individuazione di servizi diretti WiFi

La classe `WifiP2pManager` è stata introdotta in Android 4,0 per supportare *Zeroconf*. Zeroconf (Zero Configuration Networking) è un set di tecniche che consente ai dispositivi (computer, stampanti e telefoni) di connettersi automaticamente alle reti, con l'intervento degli operatori di rete umana o dei server di configurazione speciali.

In Jelly Bean `WifiP2pManager` possibile individuare i dispositivi nelle vicinanze usando *Bonjour* o *UPnP*. Bonjour è l'implementazione di Apple di Zeroconf. UPnP è un set di protocolli di rete che supporta anche Zeroconf. I metodi seguenti sono stati aggiunti alla `WiFiP2pManager` per supportare l'individuazione del servizio Wi-Fi:

- `AddLocalService()`: questo metodo viene usato per annunciare un'applicazione come servizio tramite Wi-Fi per l'individuazione da peer.
- `AddServiceRequest(`): questo metodo consente di inviare una richiesta di individuazione del servizio al Framework. Viene usato per inizializzare l'individuazione del servizio Wi-Fi.
- `SetDnsSdResponseListeners()`: questo metodo viene usato per registrare i callback da richiamare alla ricezione di una risposta alle richieste di individuazione da Bonjour.
- `SetUpnpServiceResponseListener()`: questo metodo viene usato per registrare i callback da richiamare alla ricezione di una risposta alle richieste di individuazione UPnP.

### <a name="content-providers"></a>Provider di contenuto

La classe `ContentResolver` ha ricevuto un nuovo metodo, `AcquireUnstableContentProvider`. Questo metodo consente a un'applicazione di acquisire un provider di contenuti "instabile". In genere, quando un'applicazione acquisisce un provider di contenuti e tale provider di contenuti si arresta in modo anomalo, l'applicazione. Con questa chiamata al metodo, un'applicazione non si arresterà in modo anomalo se il provider di contenuti si blocca. Al contrario, `Android.OS.DeadObjectionException` verranno generate dalle chiamate sul provider di contenuti per informare un'applicazione che il provider di contenuti è stato eliminato. Un provider di contenuti "instabile" è utile quando si interagisce con i provider di contenuti di altre applicazioni. è meno probabile che il codice bacato da un'altra applicazione abbia effetto su un'altra applicazione.

### <a name="copy-and-paste-with-intents"></a>Copia e incolla con Intent

Alla classe `Intent` ora può essere associato un oggetto `ClipData` tramite la proprietà `Intent.ClipData`. Questo metodo consente la trasmissione di dati aggiuntivi dagli Appunti con lo scopo. Un'istanza di `ClipData` può contenere uno o più `ClipData.Item`. `ClipData.Item`sono elementi dei tipi seguenti:

- **Testo** : stringa di testo, HTML o qualsiasi stringa il cui formato è supportato dagli intervalli di stile Android incorporati.
- **Finalità** : qualsiasi oggetto `Intent`.
- **URI** : può trattarsi di qualsiasi URI, ad esempio un segnalibro http o l'URI di un provider di contenuti.

### <a name="isolated-services"></a>Servizi isolati

Un servizio isolato è un servizio che viene eseguito con il proprio processo speciale e non dispone di autorizzazioni specifiche. L'unica comunicazione con il servizio è l'avvio del servizio e l'associazione tramite l'API del servizio. È possibile dichiarare un servizio come isolato impostando la proprietà `IsolatedProcess="true"` nell'`ServiceAttribute` che adorna una classe del servizio.

### <a name="media"></a>Supporti

La nuova classe `Android.Media.MediaCodec` fornisce un'API per i codec multimediali di basso livello. Le applicazioni possono eseguire query sul sistema per scoprire quali codec di basso livello sono disponibili nel dispositivo.

Sono state aggiunte le nuove sottoclassi `Android.Media.Audiofx.AudioEffect` per supportare la pre-elaborazione audio aggiuntiva nell'audio acquisito:

- `Android.Media.Audiofx.AcousticEchoCanceler`: questa classe viene usata per la pre-elaborazione dell'audio per rimuovere il segnale da una parte remota da un segnale audio acquisito. Ad esempio, la rimozione dell'eco da un'applicazione di comunicazione vocale.
- `Android.Media.Audiofx.AutomaticGainControl`: questa classe viene usata per normalizzare il segnale acquisito aumentando o riducendo un segnale di input in modo che il segnale di output sia costante.
- `Android.Media.Audiofx.NoiseSuppressor`: questa classe eliminerà il rumore di fondo dal segnale acquisito.

Non tutti i dispositivi supporteranno questi effetti. Il metodo `AudioEffect.IsAvailable` deve essere chiamato da un'applicazione per verificare se l'effetto audio in questione è supportato nel dispositivo che esegue l'applicazione.

La classe `MediaPlayer` supporta ora la riproduzione gapless con il metodo `SetNextMediaPlayer()`. Questo nuovo metodo specifica il MediaPlayer successivo da avviare quando il lettore multimediale corrente termina la riproduzione.

Le nuove classi seguenti forniscono i meccanismi e l'interfaccia utente standard per la selezione della posizione in cui verranno riprodotti i supporti:

- `MediaRouter`: questa classe consente alle applicazioni di controllare il routing dei canali multimediali da un dispositivo a altoparlanti esterni o ad altri dispositivi.
- `MediaRouterActionProvider` e `MediaRouteButton`: queste classi consentono di fornire un'interfaccia utente coerente per la selezione e la riproduzione di file multimediali.

### <a name="notifications"></a>Notifiche

Android 4,1 consente alle applicazioni maggiore flessibilità e controllo con la visualizzazione di notifiche. Le applicazioni possono ora visualizzare notifiche più grandi e migliori agli utenti. Un nuovo metodo, `NotificationBuilder.SetStyle()` consente di impostare uno dei nuovi tre nuovi stili per le notifiche:

- `Notification.BigPictureStyle`: si tratta di una classe helper che genera notifiche in cui sarà presente un'immagine. Nell'immagine seguente viene illustrato un esempio di notifica con una grande immagine:

 [schermata di esempio![di una notifica BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`: si tratta di una classe helper che genera notifiche contenenti più righe di testo, ad esempio la posta elettronica. Un esempio di questo nuovo stile di notifica può essere visualizzato nella schermata seguente:

 [schermata di esempio![di una notifica BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`: si tratta di una classe helper che genera notifiche contenenti un elenco di stringhe, ad esempio frammenti da un messaggio di posta elettronica, come illustrato in questo screenshot:

 [schermata di esempio![di una notifica Notification. InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

È possibile aggiungere fino a due pulsanti di azione nella parte inferiore di un messaggio di notifica quando la notifica usa lo stile normale o più grande.
Un esempio può essere illustrato nello screenshot seguente, in cui i pulsanti di azione sono visibili nella parte inferiore della notifica:

 [![screenshot di esempio dei pulsanti di azione visualizzati sotto un messaggio di notifica](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La classe `Notification` ha ricevuto nuove costanti che consentono a uno sviluppatore di specificare uno dei cinque livelli di priorità per una notifica. Questi possono essere impostati in una notifica tramite la proprietà `Priority`.

### <a name="permissions"></a>Autorizzazioni

Sono state aggiunte le nuove autorizzazioni seguenti:

- `READ_EXTERNAL_STORAGE`: l'applicazione richiede l'accesso in sola lettura all'archiviazione esterna. Attualmente tutte le applicazioni dispongono dell'accesso in lettura per impostazione predefinita, ma le versioni future di Android richiedono che le applicazioni chiedano esplicitamente l'accesso in lettura.
- `READ_USER_DICTIONARY`: consente un accesso in lettura al dizionario di Word dell'utente.
- `READ_CALL_LOG`: consente a un'applicazione di ottenere informazioni sulle chiamate in ingresso e in uscita leggendo il registro chiamate.
- `WRITE_CALL_LOG`: consente a un'applicazione di scrivere nel registro chiamate sul telefono.
- `WRITE_USER_DICTIONARY`: consente a un'applicazione di scrivere nel dizionario di parole dell'utente.

Una modifica importante da notare `READ_EXTERNAL_STORAGE`: attualmente questa autorizzazione viene concessa automaticamente da Android. Le versioni future di Android richiedono che un'applicazione richieda questa autorizzazione prima di concedere l'autorizzazione.

## <a name="summary"></a>Riepilogo

In questo articolo sono state introdotte alcune delle nuove API disponibili in Android 4,1 (livello API 16). Sono state evidenziate alcune delle modifiche per le animazioni e l'animazione dell'avvio di un'attività e sono state introdotte le nuove API per l'individuazione di rete di altri dispositivi che usano protocolli come Bonjour o UPnP. Sono state evidenziate anche altre modifiche apportate all'API, ad esempio la possibilità di tagliare e incollare dati tramite Intent, la possibilità di usare servizi isolati o provider di contenuti "instabili".

Questo articolo è stato quindi introdotto per introdurre gli aggiornamenti delle notifiche e sono state illustrate alcune delle nuove autorizzazioni introdotte con Android 4,1

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione temporale (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [API Android 4,1](https://developer.android.com/about/versions/android-4.1.html)
- [Attività e stack back](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navigazione con backup e backup](https://developer.android.com/design/patterns/navigation.html)
