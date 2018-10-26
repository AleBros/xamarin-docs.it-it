---
title: Jelly Bean funzionalità
description: "Questo documento fornirà una panoramica generale delle nuove funzionalità per gli sviluppatori che sono stati introdotti in Android 4.1. Queste funzionalità includono: notifiche, gli aggiornamenti per Android fascio di condividere file di grandi dimensioni, gli aggiornamenti per l'individuazione di rete multimedia, peer-to-peer, animazioni, nuove autorizzazioni avanzate."
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 9e83c9a8c1e2740596a981598cafbbfb65e2caf2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119241"
---
# <a name="jelly-bean-features"></a>Jelly Bean funzionalità

_Questo documento fornirà una panoramica generale delle nuove funzionalità per gli sviluppatori che sono stati introdotti in Android 4.1. Queste funzionalità includono: notifiche, gli aggiornamenti per Android fascio di condividere file di grandi dimensioni, gli aggiornamenti per l'individuazione di rete multimedia, peer-to-peer, animazioni, nuove autorizzazioni avanzate._



## <a name="overview"></a>Panoramica

Android 4.1 (API livello 16), noto anche come "Jelly Bean", era versione 9 luglio 2012. Questo articolo fornirà un'introduzione approfondita ad alcune delle nuove funzionalità di Android 4.1 per gli sviluppatori che usano xamarin. Android. Alcune di queste nuove funzionalità introdotte sono miglioramenti alle animazioni per l'avvio di un'attività, nuovo file audio per una fotocamera e supporto migliorato per la navigazione tramite applicazione dello stack. È ora possibile tagliare e incollare con finalità.

La stabilità delle applicazioni Android è stata migliorata grazie alla possibilità di isolare la dipendenza da provider di contenuti instabile. Servizi potrebbero anche essere isolati in modo che siano accessibili solo tramite l'attività che li ha avviato.

È stato aggiunto supporto per l'individuazione del servizio di rete con servizi basati su Bonjour, UPnP o DNS multicast. È ora possibile per le notifiche più avanzate che hanno formato testo, pulsanti di azione e immagini di grandi dimensioni.

Infine diverse nuove autorizzazioni sono state aggiunte in Android 4.1.



## <a name="requirements"></a>Requisiti

Per sviluppare applicazioni xamarin. Android usando Jelly Bean richiede xamarin. Android 4.2.6 o versioni successive e Android 4.1 (livello API 16) essere installato tramite Android SDK Manager come illustrato nella schermata riportata di seguito:

[![Selezione di Android 4.1 in Android SDK Manager](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>Novità



### <a name="animations"></a>Animations

Le attività possono essere avviate tramite zoom animazioni o animazioni personalizzate utilizzando il `ActivityOptions` classe. Per supportare queste animazioni vengono forniti i nuovi metodi seguenti:

-   `MakeScaleUpAnimation` – Questo creerà un'animazione che garantisce la scalabilità verticale di una finestra di attività da una posizione di inizio e dimensioni sullo schermo.
-   `MakeThumbnailScaleUpAnimation` – Questo creerà un'animazione che garantisce la scalabilità verticale da un'immagine di anteprima dalla posizione specificata sullo schermo.
-   `MakeCustomAnimation` – Questo crea un'animazione da risorse nell'applicazione. C'è un'animazione per quando si apre l'attività e un altro per l'arresto dell'attività.


Il nuovo `TimeAnimator` classe fornisce un'interfaccia `TimeAnimator.ITimeListener` che può inviare una notifica di un'applicazione ogni volta che viene modificato un frame di un'animazione. Ad esempio, prendere in considerazione l'implementazione seguente del `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E adesso a usare la classe, un'istanza di `TimeAnimator` viene creato e viene impostato il listener:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Come le `TimeAnimator` istanza è in esecuzione, verrà richiamato `ITimeAnimator.ITimeListener`, che quindi l'accesso come lungo l'animatore è stato in esecuzione e quanto tempo come stato dall'ultima volta il metodo è stato richiamato.



### <a name="application-stack-navigation"></a>Navigazione tramite applicazione Stack

Android 4.1 migliora la navigazione dello stack dell'applicazione che è stato introdotto in Android 3.0. Specificando il `ParentName` proprietà del `ActivityAttribute`, Android è possibile aprire l'attività padre appropriata quando l'utente preme il [pulsante](http://developer.android.com/design/patterns/navigation.html#up-vs-back) sulla barra delle azioni - Android verrà creata un'istanza di attività specificata per il `ParentName`proprietà. Ciò consente alle applicazioni mantenere una gerarchia di attività che rendono una determinata attività.

Per la maggior parte delle applicazioni impostando la `ParentName` sull'attività sono informazioni sufficienti per Android fornire il comportamento corretto per l'esplorazione dello stack dell'applicazione; Android verrà sintetizzare stack indietro necessari creando una serie di Intent per ogni attività padre. Tuttavia, poiché si tratta di uno stack di applicazioni artificiali, ogni attività sintetico non avrà lo stato salvato che potrebbe avere un'attività naturale. Per fornire lo stato salvato per un'attività padre sintetica, un'attività può eseguire l'override di `OnPrepareNavigationUpTaskStack` (metodo). Questo metodo riceve un `TaskStackBuilder` oggetti dell'istanza che disporrà di una raccolta di Intent che Android userà per creare lo stack indietro. L'attività può modificare questi Intent in modo che, quando viene creata l'attività sintetica, riceveranno le informazioni di stato appropriato.

Per scenari più complessi, sono disponibili nuovi metodi nella classe di attività che può essere utilizzata per gestire il comportamento di navigazione e costruire lo stack indietro:

-   `OnNavigateUp` – Eseguendo l'override di questo metodo è possibile eseguire un'azione personalizzata quando le <span class="ui">backup</span> pulsante viene premuto.
-   `NavigateUpTo` -Chiamata di questo metodo causerà l'applicazione passare dall'attività corrente per l'attività specificata da un determinato scopo.
-   `ParentActivityIntent` – Questo consente di ottenere un Intent che avvia l'attività padre dell'attività corrente.
-   `ShouldUpRecreateTask` : Questo metodo viene utilizzato per eseguire una query se è necessario creare lo stack indietro sintetico per spostarsi fino a un'attività padre. Restituisce `true` se è necessario creare lo stack sintetico. 
-   `FinishAffinity` -Chiamata di questo metodo terminerà l'attività corrente e tutte le attività sotto di esso nell'attività corrente che presentano l'affinità di attività stessa.
-   `OnCreateNavigateUpTaskStack` -Questo metodo viene sottoposto a override quando è necessario avere controllo completo sulla modalità di creazione dello stack sintetico.




### <a name="camera"></a>Fotocamera

È disponibile una nuova interfaccia, `Camera.IAutoFocusMoveCallback`, che consente di rilevare quando lo stato attivo automatica è stata avviata o arrestata lo spostamento. Un esempio di questa nuova interfaccia può essere visualizzato nel frammento di codice seguente:

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

La nuova classe `MediaActionSound` offre un set di API per la produzione di suoni per le varie azioni di supporti. Sono disponibili diverse azioni che possono verificarsi con una fotocamera, questi vengono definiti dal tipo enum `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` : Questo file audio viene riprodotto quando l'attenzione è stata completata.
-   `MediaActionSoundType.ShutterClick` – Questo suono da riprodurre quando viene creata l'immagine immagine fissa.
-   `MediaActionSoundType.StartVideoRecording` : Questo file audio viene utilizzato indicare l'inizio di una registrazione video.
-   `MediaActionSoundType.StopVideoRecording` – Questo suono viene riprodotto per indicare la fine della registrazione video.


Un esempio di come usare il `MediaActionSound` classe può essere visualizzata nel frammento di codice seguente:

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



### <a name="connectivity"></a>connettività



#### <a name="android-beam"></a>Trasmetti Android

Trasmetti Android sono una tecnologia NFC basata che consente di comunicare tra loro i due dispositivi Android. Android 4.1 fornisce un supporto migliore per il trasferimento dei file di grandi dimensioni. Quando si usa il nuovo metodo `NfcAdapter.SetBeamPushUris()` Android passa tra i meccanismi di trasporto alternativo (ad esempio Bluetooth) per ottenere una velocità di trasferimento rapido.



#### <a name="network-services-discovery"></a>Individuazione di servizi di rete

Android 4.1 contiene nuove API per l'individuazione del servizio multicast basati su DNS.
In questo modo un'applicazione rilevare e connettersi tramite Wi-Fi ad altri dispositivi, ad esempio stampanti, fotocamere e dispositivi multimediali. Queste nuove API sono le `Android.Net.Nsd` pacchetto.

Per creare un servizio che può essere usato da altri servizi, il `NsdServiceInfo` classe viene utilizzata per creare un oggetto che definisce le proprietà di un servizio. Questo oggetto viene quindi fornito `NsdManager.RegisterService()` insieme a un'implementazione di `NsdManager.ResolveListener`. Le implementazioni di `NsdManager.ResolveListener` vengono utilizzate per inviare una notifica di una registrazione corretta e annullare la registrazione del servizio.

Per individuare servizi nella rete e implementazione di `Nsd.DiscoveryListener` passato a `NsdManager.discoverServices()`.



#### <a name="network-usage"></a>Utilizzo della rete

Un nuovo metodo, `ConnectivityManager.IsActiveNetworkMetered` consente a un dispositivo verificare se è connesso a una rete a consumo. Questo metodo può essere utilizzato per aiutare a gestire in modo accurato per informare gli utenti che potrebbero essere presenti costosa addebiti per le operazioni sui dati consumo dei dati.



#### <a name="wifi-direct-service-discovery"></a>Individuazione del servizio Wi-Fi Direct

Il `WifiP2pManager` classe è stata introdotta in Android 4.0 per supportare *zeroconf*. Zeroconf (rete zero configuration) è un set di tecniche che consente ai dispositivi (computer, stampanti, telefoni) per connettersi automaticamente alle reti con l'intervento di operatori umani rete o server di configurazione speciale.

In Jelly Bean, `WifiP2pManager` può individuare i dispositivi usando circostanti *Bonjour* oppure *Upnp*. Bonjour è l'implementazione di Apple di zeroconf. UPnP è set di protocolli di rete che supporta anche zeroconf. I seguenti metodi aggiunti al `WiFiP2pManager` per supportare l'individuazione del servizio Wi-Fi:

-   `AddLocalService()` : Questo metodo viene utilizzato annunciare un'applicazione come servizio tramite Wi-Fi per l'individuazione da peer.
-   `AddServiceRequest(` ): Questo metodo consiste nell'inviare una richiesta di individuazione del servizio per il framework. E viene usato per inizializzare l'individuazione del servizio Wi-Fi.
-   `SetDnsSdResponseListeners()` : Questo metodo viene utilizzato per registrare i callback da richiamare quando riceve una risposta alle richieste di individuazione di Bonjour.
-   `SetUpnpServiceResponseListener()` : Questo metodo viene utilizzato per registrare i callback da richiamare quando riceve una risposta alle richieste di individuazione Upnp.




### <a name="content-providers"></a>Provider di contenuti

Il `ContentResolver` classe ha ricevuto un nuovo metodo, `AcquireUnstableContentProvider`. Questo metodo consente a un'applicazione acquisire un provider di contenuti "instabile". In genere, quando un'applicazione acquisisca un provider di contenuti e gli arresti anomali di tale provider di contenuti, pertanto, sarà l'applicazione. Con questa chiamata al metodo, un'applicazione non viene interrotta se si blocca al provider di contenuti. Al contrario, `Android.OS.DeadObjectionException` verranno generate da chiamate nel provider di contenuti per informare un'applicazione che il provider di contenuti è stata chiusa. Un provider di contenuti "instabile" è utile quando si interagisce con i provider di contenuti da altre applicazioni, è meno probabile che un codice difettoso da un'altra applicazione avrà effetto su un'altra applicazione.



### <a name="copy-and-paste-with-intents"></a>Copia e Incolla con finalità

Il `Intent` classe può ora includere una `ClipData` oggetto associato a quest'ultima con il `Intent.ClipData` proprietà. Questo metodo consente aggiuntiva dei dati dagli Appunti devono essere trasmessi con lo scopo. Un'istanza di `ClipData` può contenere uno o più `ClipData.Item`. `ClipData.Item`di elementi dei tipi seguenti:

-   **Testo** : si tratta di qualsiasi stringa di testo, entrambi HTML o si estende su qualsiasi stringa il cui formato è supportato dallo stile predefinito di Android.
-  **Finalità** -qualsiasi `Intent` oggetto.
-   **URI** : può trattarsi di qualsiasi URI, ad esempio un segnalibro HTTP o l'URI per un provider di contenuti.




### <a name="isolated-services"></a>Servizi di tipo isolati

Un servizio di tipo isolato è un servizio che viene eseguito con un proprio processo speciale e non dispone delle autorizzazioni di un proprio. L'unica comunicazione con il servizio è quando avvia il servizio e il binding ad esso tramite l'API del servizio. È possibile dichiarare un servizio come isolato impostando la proprietà `IsolatedProcess="true"` nella `ServiceAttribute` che da decorare una classe di servizio.


### <a name="media"></a>Supporti

Il nuovo `Android.Media.MediaCodec` classe fornisce un'API per i codec di media a basso livello. Le applicazioni possono richiedere al sistema per scoprire quali i codec di basso livelli sono disponibili sul dispositivo.

Il nuovo `Android.Media.Audiofx.AudioEffect` sottoclassi sono stati aggiunti per supportare audio aggiuntivo nei file audio di pre-elaborazione:

-   `Android.Media.Audiofx.AcousticEchoCanceler` : Questa classe viene utilizzata per pre-elaborazione audio per rimuovere il segnale da una parte remota da un segnale audio acquisita. Ad esempio, rimuovendo l'eco da un'applicazione di comunicazione vocale.
-   `Android.Media.Audiofx.AutomaticGainControl` : Questa classe viene utilizzata per normalizzare il segnale acquisito da incrementare o ridurre un segnale di input in modo che il segnale di output è costante.
-   `Android.Media.Audiofx.NoiseSuppressor` : Questa classe rimuoverà radiazione di fondo dal segnale acquisito.


Non tutti i dispositivi supporterà questi effetti. Il metodo `AudioEffect.IsAvailable` deve essere chiamato da un'applicazione per verificare se l'effetto di audio in questione è supportato per i dispositivi con l'applicazione.

Il `MediaPlayer` classe ora supporta la riproduzione senza pausa con la `SetNextMediaPlayer()` (metodo). Questo nuovo metodo specifica MediaPlayer successivo per l'avvio quando il lettore multimediale corrente termina la riproduzione.

Le nuove classi seguenti forniscono meccanismi standard e l'interfaccia utente per la selezione in cui verranno riprodotto multimediali:

-   `MediaRouter` : Questa classe consente alle applicazioni di controllare il routing dei canali di supporto da un dispositivo da relatori esterni o altri dispositivi.
-   `MediaRouterActionProvider` e `MediaRouteButton` – queste classi consentono di fornire un'interfaccia utente coerente per la selezione e la riproduzione multimediale.




### <a name="notifications"></a>Notifiche

Android 4.1 consente alle applicazioni maggiore flessibilità e controllo con visualizzazione delle notifiche. Le applicazioni possono ora visualizzare le notifiche più ampie e migliori agli utenti. Un nuovo metodo, `NotificationBuilder.SetStyle()` consente per uno dei tre nuovi nuovo stile da impostare per le notifiche:

-   `Notification.BigPictureStyle` -Si tratta di una classe helper che genera notifiche che avranno un'immagine in essi contenuti. L'immagine seguente mostra un esempio di una notifica con un'immagine di big data:


 [![Screenshot di esempio di una notifica BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` -Si tratta di una classe helper che genera notifiche che disporranno di più righe di testo, ad esempio posta elettronica. Un esempio di questo nuovo stile di notifica può essere visualizzato nello screenshot seguente:


 [![Screenshot di esempio di una notifica BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` -Si tratta di una classe helper che genererà le notifiche che contengono un elenco di stringhe, ad esempio i frammenti di codice da un messaggio di posta elettronica, come illustrato in questo screenshot:


 [![Screenshot di esempio di una notifica Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

È possibile aggiungere fino a due pulsanti di azione nella parte inferiore di un messaggio di notifica quando la notifica Usa lo stile normale o superiori.
Un esempio di questo può essere visualizzato nello screenshot seguente, in cui i pulsanti di azione sono visibili nella parte inferiore della notifica:

 [![Screenshot di esempio di pulsanti di azione visualizzati di sotto di un messaggio di notifica](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

Il `Notification` classe ha ricevuto nuove costanti che consentono agli sviluppatori di specificare uno dei cinque livelli di priorità per una notifica. Possono essere impostate su una notifica utilizzando il `Priority` proprietà.



### <a name="permissions"></a>Autorizzazioni

Sono state aggiunte le nuove autorizzazioni seguenti:

-   `READ_EXTERNAL_STORAGE` -L'applicazione richiede accesso di sola lettura alla risorsa di archiviazione esterna. Attualmente tutte le applicazioni hanno accesso in lettura per impostazione predefinita, ma le versioni future di Android richiederanno le applicazioni richiedono in modo esplicito l'accesso in lettura.
-   `READ_USER_DICTIONARY` : Consente un accesso in lettura a dizionario dell'utente.
-   `READ_CALL_LOG` -Consente a un'applicazione ottenere informazioni sulle chiamate in ingresso e in uscita per la lettura del log di chiamata.
-   `WRITE_CALL_LOG` -Consente a un'applicazione scrivere nel log di chiamata sul telefono.
-   `WRITE_USER_DICTIONARY` -Consente a un'applicazione in cui scrivere dizionario dell'utente.


Un'importante modifica notare `READ_EXTERNAL_STORAGE` – attualmente questa autorizzazione viene concessa automaticamente da Android. Le future versioni di Android richiederanno un'applicazione per richiedere l'autorizzazione prima di concedere l'autorizzazione.



## <a name="summary"></a>Riepilogo

Questo articolo descrive alcune delle nuove API che sono disponibili in Android 4.1 (livello API 16). Evidenziate alcune delle modifiche per le animazioni e animare l'avvio di un'attività e introdotta l'API nuova per l'individuazione di rete di altri dispositivi tramite protocolli, quali Bonjour e UPnP. Altre modifiche all'API sono state evidenziate, ad esempio la possibilità di tagliare e incollare i dati tramite gli Intent, possibilità di utilizzare servizi di tipo isolati o provider di contenuti "instabile".

In questo articolo è verificato un errore per introdurre gli aggiornamenti per le notifiche e sono state descritte alcune delle nuove autorizzazioni che sono state introdotte con Android 4.1


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione di tempo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 le API](http://developer.android.com/about/versions/android-4.1.html)
- [Le attività e stack indietro](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navigazione con pulsanti Indietro e backup](http://developer.android.com/design/patterns/navigation.html)
