---
title: Caratteristiche di Torrone
description: Come iniziare a utilizzare Xamarin.Android per sviluppare applicazioni per Android Torrone.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 6274c75abf229268070d495ced662724f5c16627
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027095"
---
# <a name="nougat-features"></a>Caratteristiche di Torrone

_Come iniziare a utilizzare Xamarin.Android per sviluppare applicazioni per Android Torrone._

Questo articolo fornisce una struttura delle funzionalità introdotte in Android Torrone, spiega come preparare lo sviluppo di Xamarin.Android per Android Nougat e fornisce collegamenti ad applicazioni di esempio che illustrano come usare le funzionalità di Android Nougat nelle app Xamarin.Android.

## <a name="overview"></a>Panoramica

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) è il seguito di Google per Android 6.0 Marshmallow. Xamarin.Android fornisce il supporto per **Android 7.x Bindings** in Xamarin Android 7.0 e versioni successive. Android Nougat aggiunge molte nuove API per le funzionalità Di torrone descritte di seguito; queste API sono disponibili per le app Xamarin.Android quando si utilizza Xamarin.Android 7.0.

[![Immagini eroe di tablet e telefoni Android con Android Torrone](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Per ulteriori informazioni sulle API di Android 7.x, vedere [Android 7.1 per sviluppatori](https://developer.android.com/preview/api-overview.html).
Per un elenco dei problemi noti di Xamarin.Android 7.0, vedere le [note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

Android Nougat offre molte nuove funzionalità di interesse per gli sviluppatori Xamarin.Android. Queste funzionalità includono:

- **Supporto** &ndash; multi-finestra Questo miglioramento consente agli utenti di aprire due app sullo schermo contemporaneamente.

- Miglioramenti delle notifiche Il sistema di notifiche riprogettato in Android Nougat include una funzionalità *di risposta diretta* che consente agli utenti di rispondere rapidamente **ai** &ndash; messaggi di testo direttamente dall'interfaccia utente di notifica. Inoltre, se l'app crea notifiche per i messaggi ricevuti, la nuova funzionalità di *notifiche in bundle* può raggruppare le notifiche come un singolo gruppo quando viene ricevuto più di un messaggio.

- **Risparmio dati** &ndash; Questa funzionalità è un nuovo servizio di sistema che consente di ridurre l'utilizzo della rete dati da parte delle app; offre agli utenti il controllo su come le app utilizzano la rete dati.

Inoltre, Android Nougat offre molti altri miglioramenti di interesse per gli sviluppatori di app, ad esempio una nuova funzionalità di configurazione della sicurezza di rete, Doze on the Go, attestazione chiave, nuove API delle impostazioni rapide, supporto per più impostazioni locali, API ICU4J, miglioramenti di WebView, accesso alle funzionalità del linguaggio Java 8, accesso alla directory con ambito, un'API del puntatore personalizzato, supporto VR della piattaforma, file virtuali e ottimizzazioni di elaborazione in background.

Questo articolo spiega come iniziare a creare app con Android Torrone per provare le nuove funzionalità e pianificare la migrazione o il lavoro per la nuova piattaforma Android Torrone.

## <a name="requirements"></a>Requisiti

Per utilizzare le nuove funzionalità di Android Nougat nelle app basate su Xamarin è necessario quanto segue:

- **Visual Studio o Visual Studio per Mac** &ndash; Se si utilizza Visual Studio, è necessaria la versione 4.2.0.628 o successiva di Visual Studio Tools per Xamarin. Se si usa Visual Studio per Mac, è necessaria la versione 6.1.0 o successiva di Visual Studio per Mac.

- **Xamarin.Android** &ndash; Xamarin.Android 7.0 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- **Android SDK** - Android SDK 7.0 (API 24) o versione successiva deve essere installato tramite Android SDK Manager.

- **Lo** &ndash; sviluppo di Java Developer Kit Xamarin Android 7.0 richiede [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versioni successive se si sta sviluppando per il livello API 24 o superiore (JDK 8 supporta anche livelli API precedenti a 24). La versione a 64 bit di JDK 8 è necessaria se si utilizzano controlli personalizzati o Forms Previewer.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

Tieni presente che le app devono essere ricostruite con Xamarin C6SR4 o versioni successive per funzionare in modo affidabile con Android Torrone. Poiché Android Nougat può collegarsi solo a librerie native fornite da [NDK,](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)le app esistenti che utilizzano librerie come **Mono.Data.Sqlite.dll** potrebbero bloccarsi durante l'esecuzione su Android Nougat se non vengono ricompilate correttamente.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android Nougat con Xamarin.Android, devi scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare un progetto Android Nougat:

1. Installare gli ultimi aggiornamenti Xamarin.Android da Xamarin.

2. Installare i pacchetti e gli strumenti di **Android 7.0 (API 24)** o versione successiva.

3. Creare un nuovo progetto Xamarin.Android destinato a Android Torrone.Create a new Xamarin.Android project that targets Android Tougat.

4. Configurare un emulatore o un dispositivo per Android Nougat.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:Each of these steps is explained in the following sections:

### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Xamarin

Per aggiungere il supporto Xamarin per Android Nougat, modificare il canale degli aggiornamenti in Visual Studio o Visual Studio per Mac nel canale Stable e applicare gli aggiornamenti più recenti. Se hai bisogno anche di funzioni attualmente disponibili solo nel canale Alpha o Beta, puoi passare al canale Alpha o Beta (i canali Alpha e Beta forniscono anche il supporto per Android 7.x). Per informazioni su come modificare il canale degli aggiornamenti (rilasci), vedere [Modifica del canale degli aggiornamenti](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).

### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con Xamarin Android 7.0, devi prima usare Android SDK Manager per installare **SDK Platform Android N (API 24)** o versione successiva. È inoltre necessario installare gli strumenti SDK di **Android**più recenti:

1. Avviare Android SDK Manager (in Visual Studio per Mac, usare **strumenti > Aprire Android SDK Manager&hellip;**; in Visual Studio usare Strumenti > Android > Android SDK **Manager**).

2. Installare Android 7.0 (API 24) o versione successiva:Install **Android 7.0 (API 24)** or later:

    [![Selezione dei pacchetti Android 7.0 in Android SDK Manager](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. Installare gli strumenti più recenti di Android SDK:

    [![Selezione degli strumenti più recenti di Android SDK in Android SDK Manager](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    È necessario installare la revisione di Android SDK Tools 25.2.2 o versione successiva, gli strumenti della piattaforma Android SDK 24.0.3 o versione successiva e gli strumenti di compilazione di Android SDK 24.0.2 o versione successiva.

4. Verificare che il percorso del kit di **sviluppo Java** sia configurato per JDK 1.8:

    [![Configurazione del percorso JDK 8 in Opzioni strumenti](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Per visualizzare questa impostazione in Visual Studio, fare clic su **Strumenti > Opzioni > Impostazioni di Xamarin > Android**. In Visual Studio per Mac fare clic su **Preferenze > Projects > SDK Locations > Android**.

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Xamarin.Android

Creare un nuovo progetto Xamarin.Android.Create a new Xamarin.Android project. Se non si ha familiarità con lo sviluppo Android con Xamarin, vedere Hello, Android per informazioni sulla creazione di progetti Xamarin.Android.If you are new to Android development with Xamarin, see [Hello, Android](~/android/get-started/hello-android/index.md) to learn about creating Xamarin.Android projects.

Quando crei un progetto Android, devi configurare le impostazioni della versione per Android 7.0 o versioni successive. Ad esempio, per impostare come destinazione il progetto per Android 7.0, è necessario configurare il livello API Android di destinazione del progetto su **Android 7.0 (API 24 - Nougat).** È consigliabile impostare il livello del framework di destinazione su API 24 o versione successiva. Per altre informazioni sulla configurazione dei livelli di API Android, vedere [Informazioni sui livelli](~/android/app-fundamentals/android-api-levels.md)di API Android .

> [!NOTE]
> Attualmente è necessario impostare la **versione minima di Android** su Android **7.0 (API 24 - Nougat)** per distribuire l'app ai dispositivi O emulatori di torrone Android.

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o un dispositivo

Se si utilizza un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo utilizzando le seguenti impostazioni:

- Dispositivo: Nexus 5X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9 o Pixel C.
- Destinazione: Android 7.0 - Livello API 24Target: Android 7.0 - API Level 24
- ABI: x86 o\_x86 64

Ad esempio, questo dispositivo virtuale è configurato per emulare un Nexus 6:For example, this virtual device is configured to emulate a Nexus 6:

[![Configurazione di un AVD utilizzando un dispositivo Nexus 6, un target Android 7.0 e una CPU Intel Atom x86/ABI](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se si utilizza un dispositivo fisico, ad esempio un Nexus 5X, 6 o 9, è possibile aggiornare il dispositivo tramite aggiornamenti automatici via aria (OTA) o scaricare un'immagine del sistema e lampeggiare direttamente il dispositivo. Per ulteriori informazioni sull'aggiornamento manuale del dispositivo ad Android Nougat, vedere [Immagini OTA per dispositivi Nexus](https://developers.google.com/android/nexus/ota).

Si noti che i dispositivi Nexus 5 non sono supportati da Android Nougat.

## <a name="new-features"></a>Nuove funzionalità

Android Nougat introduce una serie di nuove funzionalità e funzionalità, come il supporto multi-finestra, miglioramenti alle notifiche e Risparmio dati. Le sezioni seguenti evidenziano queste funzionalità e forniscono collegamenti per iniziare a usarle nell'app.

### <a name="multi-window-mode"></a>Modalità multi-finestra

La modalità multi-finestra consente agli utenti di aprire due app contemporaneamente con il supporto multitasking completo. Queste app possono essere eseguite affiancate (orizzontale) o una sopra l'altra (verticale) in modalità a schermo diviso.
Gli utenti possono trascinare un divisore tra le app per ridimensionarle e possono tagliare e incollare il contenuto tra le app. Quando due app vengono presentate in modalità a più finestre, l'attività selezionata continua a essere eseguita mentre l'attività non selezionata è in pausa ma comunque visibile. La modalità multifinestra non modifica il ciclo di vita dell'attività Android.

[![App di esempio in esecuzione in modalità a più finestre sia in verticale che in orizzontaleExample apps running in multi-window mode in both portrait and landscape](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

È possibile configurare il modo in cui le attività dell'app Xamarin.Android supportano la modalità multifinestra. Ad esempio, puoi configurare gli attributi che impostano le dimensioni minime e l'altezza e la larghezza predefinite dell'app in modalità a più finestre. È possibile utilizzare `Activity.IsInMultiWindowMode` la nuova proprietà per determinare se l'attività è in modalità a più finestre. Ad esempio:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

L'app di esempio [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) include il codice C'è che illustra come sfruttare più interfacce utente di finestra con l'app.

Per ulteriori informazioni sulla modalità multi-finestra, vedere [Supporto di più](https://developer.android.com/guide/topics/ui/multi-window.html)finestre .

### <a name="enhanced-notifications"></a>Notifiche migliorate

Android Nougat introduce un sistema di notifica riprogettato. È dotato di una nuova funzione di risposta diretta che consente agli utenti di rispondere rapidamente alle notifiche per i messaggi di testo in arrivo direttamente nell'interfaccia utente di notifica. A partire da Android 7.0, i messaggi di notifica possono essere raggruppati come un singolo gruppo quando viene ricevuto più di un messaggio. Inoltre, gli sviluppatori possono personalizzare le visualizzazioni delle notifiche, sfruttare le decorazioni di sistema nelle notifiche e sfruttare i nuovi modelli di notifica durante la generazione delle notifiche.

#### <a name="direct-reply"></a>Risposta diretta

Quando un utente riceve una notifica per il messaggio in arrivo, Android Torrone consente di rispondere al messaggio all'interno della notifica (piuttosto che aprire l'applicazione di messaggistica per inviare una risposta).
Questa funzione di risposta in linea consente agli utenti di rispondere rapidamente a un SMS o a un messaggio di testo direttamente all'interno dell'interfaccia di notifica:

[![Screenshot di una notifica con un campo Risposta diretta in linea](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Per supportare questa funzionalità nell'app, devi aggiungere azioni di *risposta inline* all'app tramite un oggetto [RemoteInput](xref:Android.App.RemoteInput) in modo che gli utenti possano rispondere tramite testo direttamente dall'interfaccia utente di notifica.
Ad esempio, il codice `RemoteInput` seguente crea un oggetto per la ricezione dell'input di testo, crea una finalità in sospeso per l'azione di risposta e crea un'azione abilitata per l'input remoto:For example, the following code builds a for receiving text input, builds a pending intent for the reply action, and creates a remote input enabled action:

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

Questa azione viene aggiunta alla notifica:This action is added to the notification:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

L'app di esempio del servizio di [messaggistica](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) `RemoteInput` include il codice C' che illustra come estendere le notifiche con un oggetto. Per altre informazioni sull'aggiunta di azioni di risposta in linea all'app per Android 7.0 o versioni successive, vedi l'argomento Risposta alle notifiche di Android.For more information about adding inline reply actions to your app for Android 7.0 or later, see the Android [Replying to Notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) topic.

#### <a name="bundled-notifications"></a>Notifiche in bundle

Android Nougat può raggruppare i messaggi di notifica (ad esempio, per argomento del messaggio) e visualizzare il gruppo anziché ogni messaggio separato.
Questa funzionalità di *notifiche in bundle* consente agli utenti di chiudere o archiviare un gruppo di notifiche in un'unica azione. L'utente può scorrere verso il basso per espandere il fascio di notifiche per visualizzare ogni notifica in dettaglio:

[![Esempio di schermata delle notifiche in bundle](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Per supportare le notifiche in bundle, l'app può usare il metodo [Builder.SetGroup](xref:Android.App.Notification.Builder.SetGroup*) per raggruppare notifiche simili. Per altre informazioni sui gruppi di notifica in bundle in Android N, vedere l'argomento Notifiche di aggregazione Android.For more information about bundled notification groups in Android N, see the Android [Bundling Notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) topic.

#### <a name="custom-views"></a>Viste personalizzate

Android Nougat consente di creare visualizzazioni di notifica personalizzate con intestazioni di notifica di sistema, azioni e layout espandibili. Per altre informazioni sulle visualizzazioni di notifica personalizzate in Android Nougat, vedere l'argomento Miglioramenti delle notifiche di Android.For more information about custom notification views in Android Nougat, see the Android [Notification Enhancements](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) topic.

### <a name="data-saver"></a>Risparmio dati

A partire da Android Nougat, gli utenti possono abilitare una nuova impostazione *di Risparmio dati* che blocca l'utilizzo dei dati in background. Questa impostazione segnala inoltre all'app di usare meno dati in primo piano, ove possibile. [ConnectivityManager](xref:Android.Net.ConnectivityManager) è stato esteso in Android Nougat in modo che l'app possa verificare se l'utente ha abilitato Risparmio dati in modo che l'app possa fare uno sforzo per limitare l'utilizzo dei dati quando Risparmio dati è abilitato.

Per altre informazioni sulla nuova funzionalità Risparmio dati in Torrone Android, vedere l'argomento Android [Ottimizzazione dell'utilizzo dell'utilizzo](https://developer.android.com/training/basics/network-ops/data-saver.html) dei dati di rete.

### <a name="app-shortcuts"></a>Tasti di scelta rapida dell'app

Android 7.1 ha introdotto una funzionalità *di scelta rapida per* le app che consente agli utenti di avviare rapidamente attività comuni o consigliate con l'app.
Per attivare il menu di scelte rapide, l'utente preme &ndash; a lungo l'icona dell'app per un secondo o più il menu viene visualizzato con una vibrazione rapida.
Rilasciando la pressa, il menu rimane:

[![Schermata di esempio del menu di scelta rapida di un'app per un'app di messaggistica](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Questa funzionalità è disponibile solo livello API 25 o superiore.
Per altre informazioni sulla nuova funzionalità Collegamenti alle app in Android 7.1, vedere l'argomento [Scelte rapide da app](https://developer.android.com/guide/topics/ui/shortcuts.html) per Android.For more information about the new App Shortcuts feature in Android 7.1, see the Android App Shortcuts topic.

### <a name="sample-code"></a>Codice di esempio

Diversi campioni Xamarin.Android sono disponibili per mostrarvi come sfruttare le funzionalità di Android Nougat:

- [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) illustra l'uso dell'API multifinestra disponibile in Android Nougat. Puoi passare dalla modalità multi-windows all'app di esempio per vedere in che modo influisce sul ciclo di vita e sul comportamento dell'app.

- [Il servizio di messaggistica](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) è `NotificationCompatManager`un servizio semplice che invia notifiche utilizzando il file . Estende inoltre la notifica `RemoteInput` con un oggetto per consentire ai dispositivi Android Nougat di rispondere tramite testo direttamente dalla notifica senza dover aprire un'app.

- [Notifiche attive](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) viene illustrato `NotificationManager` come utilizzare l'API per indicare il numero di notifiche attualmente visualizzate dall'applicazione.

- [Accesso alla directory con ambitoScoped Directory Access](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Viene illustrato come utilizzare l'API di accesso alla directory con ambito per accedere facilmente a directory specifiche. Questo serve come alternativa alla `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` dovetazione o le autorizzazioni nel manifesto.

- [Avvio diretto](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Viene illustrato come archiviare i dati in uno spazio di archiviazione crittografato con il dispositivo, sempre disponibile durante l'avvio del dispositivo sia prima che dopo l'immissione delle credenziali utente (PIN/Modello/Password).

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto Android Nougat e ha spiegato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Xamarin.Android su Android Nougat. Ha anche fornito una panoramica delle funzionalità principali disponibili in Android Nougat, con collegamenti al codice sorgente di esempio per aiutarti a iniziare a creare app per Android Torrone.

## <a name="related-links"></a>Collegamenti correlati

- [Android 7.1 per sviluppatori](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
