---
title: Funzionalità Bean gelatina
description: "Questo documento verrà fornita una panoramica di alto livello delle nuove funzionalità per gli sviluppatori che sono stati introdotti in Android 4.1. Queste funzionalità includono: avanzata notifiche, gli aggiornamenti per Android raggio di condividere file di grandi dimensioni, gli aggiornamenti per l'individuazione di rete dei dati multimediali, peer-to-peer, animazioni, nuove autorizzazioni."
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1d8068ccfc8d0f159a88704370261ec5f20d8b7c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="jelly-bean-features"></a>Funzionalità Bean gelatina

_Questo documento verrà fornita una panoramica di alto livello delle nuove funzionalità per gli sviluppatori che sono stati introdotti in Android 4.1. Queste funzionalità includono: avanzata notifiche, gli aggiornamenti per Android raggio di condividere file di grandi dimensioni, gli aggiornamenti per l'individuazione di rete dei dati multimediali, peer-to-peer, animazioni, nuove autorizzazioni._



## <a name="overview"></a>Panoramica

Android 4.1 (API livello 16), noto anche come "Bean gelatina," stato versione 9 luglio 2012. In questo articolo verrà forniscono un'introduzione di alto livello ad alcune delle nuove funzionalità di Android 4.1 per gli sviluppatori che usano xamarin. Alcune di queste nuove funzionalità introdotte sono riportati i miglioramenti per le animazioni per l'avvio di un'attività, nuovi suoni per una videocamera e supporto migliorato per la navigazione dello stack dell'applicazione. È ora possibile eseguire le operazioni Taglia e Incolla con scopi.

La stabilità di applicazioni Android è stata migliorata con la possibilità di isolare la dipendenza da provider di contenuti instabile. Servizi possono essere isolati anche in modo che siano accessibili solo tramite l'attività avviata.

È stato aggiunto supporto per l'individuazione del servizio di rete utilizzando servizi basati su Bonjour, UPnP o DNS multicast. È ora possibile per le notifiche più ricche formattato il testo, pulsanti di azione e immagini di grandi dimensioni.

Infine in Android 4.1 sono state aggiunte nuove autorizzazioni diverse.



## <a name="requirements"></a>Requisiti

Sviluppo di applicazioni xamarin utilizzando gelatina Bean richiede xamarin 4.2.6 o versione successiva e Android 4.1 (API livello 16) installato mediante Android SDK Manager come illustrato nella schermata seguente:

[![Selezione di Android 4.1 in Android SDK Manager](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>Novità



### <a name="animations"></a>Animations

Le attività possono essere avviate usando zoom animazioni o animazioni personalizzate usando la `ActivityOptions` classe. I seguenti nuovi metodi vengono forniti per supportare tali animazioni:

-   `MakeScaleUpAnimation` : Per creare un'animazione che viene ridimensionata di una finestra di attività da una posizione iniziale e dimensioni dello schermo.
-   `MakeThumbnailScaleUpAnimation` -Questa operazione crea un'animazione che passa da un'immagine di anteprima dalla posizione specificata sullo schermo.
-   `MakeCustomAnimation` : Crea un'animazione dalle risorse dell'applicazione. È presente un'animazione per quando si apre l'attività e un altro per l'arresto dell'attività.


Il nuovo `TimeAnimator` classe fornisce un'interfaccia `TimeAnimator.ITimeListener` che può inviare una notifica di un'applicazione ogni volta che cambia un frame in un'animazione. Ad esempio, considerare l'implementazione seguente del `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E l'ora di utilizzare la classe, un'istanza di `TimeAnimator` viene creato e il listener è impostato:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Come il `TimeAnimator` istanza è in esecuzione, verrà richiamato `ITimeAnimator.ITimeListener`, che verrà quindi accedere alla procedura lunga l'animatore è stato in esecuzione e il tempo come stato dall'ultima volta che il metodo è stato richiamato.



### <a name="application-stack-navigation"></a>Navigazione dello Stack dell'applicazione

Android 4.1 migliora la navigazione dello stack dell'applicazione che è stata introdotta in Android 3.0. Specificando il `ParentName` proprietà del `ActivityAttribute`, Android è possibile aprire l'attività padre appropriata quando l'utente preme il [pulsante](http://developer.android.com/design/patterns/navigation.html#up-vs-back) sulla barra delle azioni - Android verrà creata un'istanza dell'attività specificato da di `ParentName`proprietà. Ciò consente alle applicazioni mantenere una gerarchia di attività che costituiscono un'attività specifica.

Per la maggior parte delle applicazioni l'impostazione di `ParentName` l'attività è informazioni sufficienti per Android fornire il comportamento corretto per spostarsi tra gli stack di applicazione; Android verrà sintetizzare stack indietro necessarie mediante la creazione di una serie di tipi per ogni attività padre. Tuttavia, poiché si tratta di uno stack applicazione artificiale, ogni attività sintetico non avrà lo stato salvato che potrebbe avere un'attività naturale. Per fornire lo stato salvato per un'attività padre sintetica, un'attività può eseguire l'override di `OnPrepareNavigationUpTaskStack` metodo. Questo metodo riceve un `TaskStackBuilder` oggetti dell'istanza che conterrà un insieme di finalità che Android verrà utilizzato per creare lo stack indietro. L'attività può modificare questi tipi in modo che, quando viene creato l'attività sintetica, riceve le informazioni sullo stato corretto.

Per scenari più complessi, sono disponibili nuovi metodi nella classe di attività che può essere utilizzata per gestire il comportamento della navigazione e costruire lo stack indietro:

-   `OnNavigateUp` – Eseguendo l'override di questo metodo è possibile eseguire un'azione personalizzata quando il <span class="ui">backup</span> pressione del pulsante.
-   `NavigateUpTo` – Questo metodo causerà l'applicazione a cui passare l'attività specificata da un determinato intento dall'attività corrente.
-   `ParentActivityIntent` : Viene utilizzato per ottenere un intento che verrà avviata l'attività padre dell'attività corrente.
-   `ShouldUpRecreateTask` : Questo metodo viene utilizzato per eseguire una query se è necessario creare sintetico stack indietro per spostarsi fino a un'attività padre. Restituisce `true` se lo stack sintetico deve essere creato. 
-   `FinishAffinity` – Questo metodo verrà completata l'attività corrente e tutte le attività sottostanti nell'attività corrente che presentano l'affinità di attività stessa.
-   `OnCreateNavigateUpTaskStack` : Questo metodo viene sottoposto a override quando è necessario disporre di controllo completo sulla modalità di creazione dello stack sintetico.




### <a name="camera"></a>Fotocamera

È una nuova interfaccia `Camera.IAutoFocusMoveCallback`, che può essere usato per rilevare quando la messa a fuoco automatica è avviato o arrestato lo spostamento. Un esempio di questa nuova interfaccia può essere visti nel frammento riportato di seguito:

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

La nuova classe `MediaActionSound` fornisce un set di API per la creazione di file audio per le varie azioni di supporto. Sono disponibili diverse azioni che possono verificarsi con una fotocamera, questi vengono definiti dall'enumerazione `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – Questo suono viene riprodotto quando la messa a fuoco è stata completata.
-   `MediaActionSoundType.ShutterClick` – Questo suono viene riprodotto quando viene acquisita l'immagine fissa di immagine.
-   `MediaActionSoundType.StartVideoRecording` – Questo suono viene utilizzato indicano l'inizio della registrazione video.
-   `MediaActionSoundType.StopVideoRecording` – Questo suono viene riprodotto per indicare la fine di una registrazione video.


Un esempio di come utilizzare la `MediaActionSound` classe può essere visti nel frammento riportato di seguito:

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



#### <a name="android-beam"></a>Trasmetti Android

Trasmetti Android sono una tecnologia NFC in base che consente di comunicare tra loro i due dispositivi Android. Android 4.1 fornisce un supporto migliore per il trasferimento di file di grandi dimensioni. Quando si utilizza il nuovo metodo `NfcAdapter.SetBeamPushUris()` Android passerà tra i meccanismi di trasporto alternativo (ad esempio, di Bluetooth) per ottenere una velocità di trasferimento rapido.



#### <a name="network-services-discovery"></a>Individuazione dei servizi di rete

Android 4.1 contiene nuove API per l'individuazione del servizio DNS in base multicast.
In questo modo un'applicazione rilevare e di connettersi tramite Wi-Fi con gli altri dispositivi, ad esempio stampanti, fotocamere e i dispositivi multimediali. Queste nuove API presenti il `Android.Net.Nsd` pacchetto.

Per creare un servizio che può essere utilizzato da altri servizi, la `NsdServiceInfo` classe viene utilizzata per creare un oggetto che definisce le proprietà di un servizio. Questo oggetto viene fornito a `NsdManager.RegisterService()` insieme a un'implementazione di `NsdManager.ResolveListener`. Le implementazioni di `NsdManager.ResolveListener` vengono utilizzate per inviare una notifica di una corretta registrazione e annullare la registrazione del servizio.

Per individuare i servizi della rete e implementazione di `Nsd.DiscoveryListener` passato a `NsdManager.discoverServices()`.



#### <a name="network-usage"></a>Utilizzo della rete

Un nuovo metodo `ConnectivityManager.IsActiveNetworkMetered` consente a un dispositivo verificare se è connesso a una rete a consumo. Questo metodo può essere utilizzato per gestire l'utilizzo di dati in modo accurato informa gli utenti che potrebbe essere costosa addebiti per le operazioni di dati.



#### <a name="wifi-direct-service-discovery"></a>Individuazione del servizio Wi-Fi Direct

Il `WifiP2pManager` classe è stata introdotta in Android 4.0 per supportare *zeroconf*. Zeroconf (rete zero configuration) è un set di tecniche che consente ai dispositivi (computer, stampanti, telefoni cellulari) per connettersi automaticamente alle reti con l'intervento di operatori di risorse umane di rete o server di configurazione speciale.

In Bean gelatina, `WifiP2pManager` può individuare nelle vicinanze di dispositivi utilizzando *Bonjour* o *Upnp*. Bonjour è l'implementazione di Apple di zeroconf. UPnP è set di protocolli di rete che supporta anche zeroconf. I metodi seguenti, aggiungervi il `WiFiP2pManager` per supportare l'individuazione del servizio Wi-Fi:

-   `AddLocalService()` : Questo metodo viene utilizzato per annunciare un'applicazione come un servizio tramite Wi-Fi per l'individuazione da peer.
-   `AddServiceRequest(` ): Questo metodo è per inviare una richiesta di individuazione del servizio per il framework. Utilizzato per inizializzare l'individuazione del servizio Wi-Fi.
-   `SetDnsSdResponseListeners()` : Questo metodo viene utilizzato per registrare i callback da richiamare quando riceve una risposta a richieste di individuazione da Bonjour.
-   `SetUpnpServiceResponseListener()` : Questo metodo viene utilizzato per registrare i callback da richiamare quando riceve una risposta alle richieste di individuazione Upnp.




### <a name="content-providers"></a>I provider di contenuti

Il `ContentResolver` classe ha ricevuto un nuovo metodo `AcquireUnstableContentProvider`. Questo metodo consente a un'applicazione di acquisire un provider di contenuti "instabile". In genere, quando un'applicazione acquisisce un provider di contenuti e il provider di contenuti si blocca, così come l'applicazione. Con questa chiamata al metodo, un'applicazione non viene interrotta se si blocca il provider di contenuti. In alternativa, `Android.OS.DeadObjectionException` verrà generata da chiamate nel provider di contenuti per informare un'applicazione che il provider di contenuti è stata chiusa. Un provider di contenuti "instabile" è utile quando si interagisce con i provider di contenuti da altre applicazioni, è meno probabile che il codice a un'altra applicazione avrà effetto su un'altra applicazione.



### <a name="copy-and-paste-with-intents"></a>Copia e Incolla con intenti

Il `Intent` classe ora è possibile avere un `ClipData` oggetto associato tramite il `Intent.ClipData` proprietà. Questo metodo consente di dati aggiuntivi dagli Appunti per essere trasmesso con lo scopo. Un'istanza di `ClipData` può contenere uno o più `ClipData.Item`. `ClipData.Item`di elementi dei tipi seguenti:

-   **Testo** : si tratta di qualsiasi stringa di testo, entrambi HTML o si estende su qualsiasi stringa il cui formato è supportato dallo stile di Android incorporato.
-  **Finalità** -qualsiasi `Intent` oggetto.
-   **URI** : può trattarsi di qualsiasi URI, ad esempio un segnalibro HTTP o l'URI per un provider di contenuti.




### <a name="isolated-services"></a>Servizi di tipo isolati

Un servizio di tipo isolato è un servizio che viene eseguito con un proprio processo speciale e non dispone di autorizzazioni di un proprio. La comunicazione sola con il servizio è quando avvio del servizio e l'associazione a esso tramite l'API del servizio. È possibile dichiarare un servizio di tipo isolato impostando la proprietà `IsolatedProcess="true"` nel `ServiceAttribute` che decora una classe di servizio.


### <a name="media"></a>Supporti

Il nuovo `Android.Media.MediaCodec` classe fornisce un'API per i codec di basso livello di supporto. Le applicazioni possono eseguire query di sistema per scoprire quali codec di basso livello sono disponibili nel dispositivo.

Il nuovo `Android.Media.Audiofx.AudioEffect` sottoclassi sono stati aggiunti per supportare ulteriore audio nei file audio di pre-elaborazione:

-   `Android.Media.Audiofx.AcousticEchoCanceler` : Questa classe viene utilizzata per pre-elaborazione audio per rimuovere il segnale da una parte remota da un segnale audio acquisito. Ad esempio la rimozione di echo da un'applicazione di comunicazione vocale.
-   `Android.Media.Audiofx.AutomaticGainControl` : Questa classe viene utilizzata per normalizzare il segnale acquisito da incrementare o ridurre un segnale di input in modo che il segnale di output è costante.
-   `Android.Media.Audiofx.NoiseSuppressor` : Questa classe rimuoverà rumore dal segnale acquisito.


Non tutti i dispositivi supporterà questi effetti. Il metodo `AudioEffect.IsAvailable` deve essere chiamato da un'applicazione per verificare se l'effetto audio in questione è supportato sul dispositivo che esegue l'applicazione.

Il `MediaPlayer` classe ora supporta la riproduzione senza pausa con il `SetNextMediaPlayer()` metodo. Questo nuovo metodo specifica MediaPlayer successivo per l'avvio quando il lettore multimediale corrente termina la riproduzione.

Nuove classi seguenti forniscono i meccanismi standard e l'interfaccia utente per la selezione in cui verranno riprodotto supporti:

-   `MediaRouter` : Questa classe consente alle applicazioni di controllare il routing dei canali di supporto da un dispositivo per altoparlanti esterni o altri dispositivi.
-   `MediaRouterActionProvider` e `MediaRouteButton` – queste classi consentono di fornire un'interfaccia utente coerente per la selezione e la riproduzione multimediale.




### <a name="notifications"></a>Notifiche

Android 4.1 consente alle applicazioni di maggiore flessibilità e controllo con la visualizzazione di notifiche. Le applicazioni possono ora mostrano la notifiche agli utenti. Un nuovo metodo `NotificationBuilder.SetStyle()` consente per uno dei tre nuovi nuovo stile da impostare per le notifiche:

-   `Notification.BigPictureStyle` : Si tratta di una classe helper che genera notifiche che disporranno di un'immagine in essi contenuti. Nella figura seguente viene illustrato un esempio di una notifica a un'immagine di grandi dimensioni:


 [![Schermata di esempio di una notifica BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` : Si tratta di una classe helper che genera notifiche che disporranno di più righe di testo, ad esempio posta elettronica. Un esempio di questo nuovo stile di notifica può essere visualizzato nella schermata seguente:


 [![Schermata di esempio di una notifica BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` : Si tratta di una classe helper che genera notifiche che contengono un elenco di stringhe, ad esempio i frammenti di codice da un messaggio di posta elettronica, come illustrato in questo screenshot:


 [![Schermata di esempio di una notifica Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

È possibile aggiungere fino a due pulsanti di azione nella parte inferiore di un messaggio di notifica quando la notifica è usando lo stile normale o superiori.
Un esempio di questo può essere visualizzato nella schermata seguente, in cui i pulsanti di azione sono visibili nella parte inferiore della notifica:

 [![Schermata di esempio di pulsanti di azione visualizzati di sotto di un messaggio di notifica](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La `Notification` classe ha ricevuto nuove costanti che consentono a uno sviluppatore di specificare uno dei cinque livelli di priorità per la notifica. È possibile impostarli su un notifica tramite il `Priority` proprietà.



### <a name="permissions"></a>Autorizzazioni

Sono state aggiunte le nuove autorizzazioni seguenti:

-   `READ_EXTERNAL_STORAGE` -L'applicazione richiede l'accesso in sola lettura in un archivio esterno. Attualmente tutte le applicazioni dispongono dell'accesso in lettura per impostazione predefinita, ma le versioni future di Android richiederanno applicazioni richiedono esplicitamente l'accesso in lettura.
-   `READ_USER_DICTIONARY` -Consente un accesso in lettura dizionario dell'utente.
-   `READ_CALL_LOG` -Consente a un'applicazione ottenere informazioni sulle chiamate in ingresso e in uscita, consultare il registro delle chiamate.
-   `WRITE_CALL_LOG` -Consente a un'applicazione scrivere nel log di chiamata al telefono.
-   `WRITE_USER_DICTIONARY` -Consente a un'applicazione in cui scrivere dizionario dell'utente.


Una modifica importante notare `READ_EXTERNAL_STORAGE` – attualmente questa autorizzazione viene concessa automaticamente da Android. Versioni future di Android richiederanno un'applicazione per richiedere l'autorizzazione prima dell'autorizzazione.



## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto alcune delle nuove API disponibili in Android 4.1 (API livello 16). Evidenziate alcune delle modifiche per animazioni e l'avvio di un'attività di animazione e introdotte le nuove API di per l'individuazione di rete di altri dispositivi utilizzando protocolli, ad esempio Bonjour o UPnP. Altre modifiche all'API sono state evidenziate, ad esempio la possibilità per tagliare e incollare i dati tramite intenti, la possibilità di utilizzare servizi isolati o i provider di contenuti "instabili".

In questo articolo è passato introdurre gli aggiornamenti per le notifiche e illustrate alcune delle nuove autorizzazioni che sono stati introdotti con Android 4.1


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione ora (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 API](http://developer.android.com/about/versions/android-4.1.html)
- [Attività e stack indietro](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navigazione con pulsanti Indietro e backup](http://developer.android.com/design/patterns/navigation.html)
