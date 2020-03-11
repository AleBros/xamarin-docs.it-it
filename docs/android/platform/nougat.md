---
title: Funzionalità torrone
description: Come iniziare a usare Novell. Android per sviluppare app per Android torrone.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 6274c75abf229268070d495ced662724f5c16627
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027095"
---
# <a name="nougat-features"></a>Funzionalità torrone

_Come iniziare a usare Novell. Android per sviluppare app per Android torrone._

Questo articolo fornisce una descrizione delle funzionalità introdotte in Android torrone, spiega come preparare Novell. Android for Android torrone Development e fornisce collegamenti ad applicazioni di esempio che illustrano come usare le funzionalità torrone di Android in App Novell. Android.

## <a name="overview"></a>Panoramica

Il [torrone Android](https://developer.android.com/about/versions/nougat/android-7.0.html) è il follow-up di Google per Android 6,0 marshmallow. Novell. Android fornisce il supporto per le **associazioni Android 7. x** in novell Android 7,0 e versioni successive. Il torrone Android aggiunge molte nuove API per le funzionalità torrone descritte di seguito. Queste API sono disponibili per le app Novell. Android quando si usa Novell. Android 7,0.

[![le immagini Hero di Tablet e telefoni Android che eseguono il torrone Android](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Per altre informazioni sulle API Android 7. x, vedere [android 7,1 per sviluppatori](https://developer.android.com/preview/api-overview.html).
Per un elenco dei problemi noti di Novell. Android 7,0, vedere le [Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

Il torrone Android offre molte nuove funzionalità interessanti per gli sviluppatori Novell. Android. Queste funzionalità comprendono:

- **Supporto** di più finestre &ndash; questa funzionalità avanzata consente agli utenti di aprire contemporaneamente due app sullo schermo.

- I **miglioramenti** apportati alle notifiche &ndash; il sistema di notifiche riprogettato in Android torrone includono una funzionalità di *risposta diretta* che consente agli utenti di rispondere rapidamente ai messaggi di testo direttamente dall'interfaccia utente di notifica. Inoltre, se l'app crea notifiche per i messaggi ricevuti, la nuova funzionalità di *notifica in bundle* può raggruppare le notifiche come un singolo gruppo quando viene ricevuto più di un messaggio.

- **Data Saver** &ndash; questa funzionalità è un nuovo servizio di sistema che consente di ridurre l'utilizzo dei dati cellulari da app; consente agli utenti di controllare la modalità di utilizzo dei dati cellulari da parte delle app.

Android torrone, inoltre, apporta molti altri miglioramenti di interesse per gli sviluppatori di app, ad esempio una nuova funzionalità di configurazione della sicurezza di rete, un servizio di attestazione delle chiavi, nuove API per le impostazioni rapide, supporto per più impostazioni locali, API ICU4J, miglioramenti di WebView, accesso alle funzionalità del linguaggio Java 8, all'accesso alla directory con ambito, a un'API puntatore personalizzata, al supporto per la piattaforma VR, ai file virtuali e alle ottimizzazioni di elaborazione in background.

Questo articolo illustra come iniziare a creare app con il torrone Android per provare le nuove funzionalità e pianificare la migrazione o il lavoro delle funzionalità per la nuova piattaforma per i torrone Android.

## <a name="requirements"></a>Requisiti

Di seguito sono riportate le funzionalità necessarie per usare le nuove funzionalità del torrone Android nelle app basate su Novell:

- **Visual Studio o Visual Studio per Mac** &ndash; se si usa Visual Studio, la versione 4.2.0.628 o successiva di strumenti di Visual Studio per Novell è obbligatoria. Se si usa Visual Studio per Mac, è richiesta la versione 6.1.0 o successiva di Visual Studio per Mac.

- **Novell. android** &ndash; Novell. Android 7,0 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- **Android SDK** -Android SDK 7,0 (API 24) o versione successiva deve essere installato tramite il Android SDK Manager.

- **Java Developer Kit** &ndash; lo sviluppo di novell Android 7,0 richiede [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sta sviluppando per livello API 24 o superiore (JDK 8 supporta anche i livelli API precedenti a 24). La versione a 64 bit di JDK 8 è obbligatoria se si usano controlli personalizzati o il Visualizzatore anteprima moduli.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

Si noti che le app devono essere ricompilate con Novell C6SR4 o versione successiva per funzionare in modo affidabile con il torrone Android. Dal momento che Android torrone può collegarsi solo a [librerie native fornite da NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), le app esistenti che usano librerie come **mono. Data. sqlite. dll** potrebbero arrestarsi in modo anomalo quando vengono eseguite su un sistema operativo Android se non vengono ricompilate correttamente.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare il torrone Android con Novell. Android, è necessario scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare un progetto Android torrone:

1. Installare gli aggiornamenti più recenti di Novell. Android dalla Novell.

2. Installare gli strumenti e i pacchetti **Android 7,0 (API 24)** o versioni successive.

3. Creare un nuovo progetto Novell. Android destinato a Android torrone.

4. Configurare un emulatore o un dispositivo per il torrone Android.

Ognuno di questi passaggi è descritto nelle sezioni seguenti:

### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Novell

Per aggiungere il supporto Novell per il torrone Android, modificare il canale degli aggiornamenti in Visual Studio o Visual Studio per Mac nel canale stabile e applicare gli aggiornamenti più recenti. Se sono necessarie anche funzionalità attualmente disponibili solo nel canale Alpha o beta, è possibile passare al canale alfa o beta (i canali alpha e beta forniscono anche il supporto per Android 7. x). Per informazioni su come modificare il canale degli aggiornamenti (versioni), vedere [modifica del canale degli aggiornamenti](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).

### <a name="install-the-android-sdk"></a>Installare il Android SDK

Per creare un progetto con Novell Android 7,0, è necessario prima di tutto usare gestione Android SDK per installare la **piattaforma SDK Android N (API 24)** o versione successiva. È necessario installare anche la **Android SDK Tools**più recente:

1. Avviare Gestione Android SDK (in Visual Studio per Mac usare **gli strumenti > aprire Android SDK manager&hellip;** . in Visual Studio usare **gli strumenti > Android > Android SDK Manager**).

2. Installare **Android 7,0 (API 24)** o versione successiva:

    [![selezionare i pacchetti Android 7,0 in gestione Android SDK](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. Installare gli strumenti di Android SDK più recenti:

    [![selezione degli strumenti di Android SDK più recenti nel gestore Android SDK](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    È necessario installare Android SDK Tools Revision 25.2.2 o versioni successive, gli strumenti della piattaforma Android SDK 24.0.3 o versione successiva e Android SDK build Tools 24.0.2 o versione successiva.

4. Verificare che il **percorso di Java Development Kit** sia configurato per JDK 1,8:

    [![configurazione del percorso JDK 8 in strumenti opzioni](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Per visualizzare questa impostazione in Visual Studio, fare clic su **strumenti > opzioni > novell > Android Settings**. In Visual Studio per Mac fare clic su **preferenze > progetti > percorsi SDK > Android**.

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Novell. Android

Creare un nuovo progetto Novell. Android. Se non si ha familiarità con lo sviluppo di Android con Novell, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti Novell. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione per la destinazione Android 7,0 o versione successiva. Ad esempio, per fare riferimento al progetto per Android 7,0, è necessario configurare il livello API Android di destinazione del progetto su **android 7,0 (API 24-torrone)** . Si consiglia di impostare il livello di Framework di destinazione su API 24 o versione successiva. Per altre informazioni sulla configurazione dei livelli a livello di API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

> [!NOTE]
> Attualmente è necessario impostare la **versione minima di Android** su **Android 7,0 (API 24-torrone)** per distribuire l'app in dispositivi o emulatori torrone Android.

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o un dispositivo

Se si usa un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo usando le impostazioni seguenti:

- Device: Nexus 5X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9 o pixel C.
- Destinazione: Android 7,0-livello API 24
- ABI: x86 o x86\_64

Ad esempio, questo dispositivo virtuale è configurato per emulare un Nexus 6:

[![configurazione di un AVD usando il dispositivo Nexus 6, la destinazione Android 7,0 e Intel Atom x86 CPU/ABI](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se si usa un dispositivo fisico, ad esempio un Nexus 5X, 6 o 9, è possibile aggiornare il dispositivo tramite gli aggiornamenti automatici via etere oppure scaricare un'immagine di sistema e lampeggiare direttamente il dispositivo. Per altre informazioni sull'aggiornamento manuale del dispositivo al torrone Android, vedere le [Immagini OTA per i dispositivi Nexus](https://developers.google.com/android/nexus/ota).

Si noti che i dispositivi Nexus 5 non sono supportati da torrone per Android.

## <a name="new-features"></a>Nuove funzionalità

Il torrone Android introduce un'ampia gamma di nuove caratteristiche e funzionalità, ad esempio il supporto per più finestre, i miglioramenti delle notifiche e il risparmio di dati. Le sezioni seguenti evidenziano queste funzionalità e forniscono collegamenti che consentono di iniziare a usarle nell'app.

### <a name="multi-window-mode"></a>Modalità multifinestra

La modalità multifinestra consente agli utenti di aprire contemporaneamente due app con il supporto completo multitasking. Queste app possono essere eseguite side-by-Side (orizzontale) o una sopra l'altra (verticale) in modalità a schermo diviso.
Gli utenti possono trascinare un divisore tra le app per ridimensionarle e possono tagliare e incollare contenuto tra le app. Quando due app vengono presentate in modalità multifinestra, l'attività selezionata continua a essere eseguita mentre l'attività non selezionata viene sospesa ma ancora visibile. La modalità multifinestra non modifica il ciclo di vita dell'attività Android.

[![app di esempio in esecuzione in modalità a più finestre sia verticale che orizzontale](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

È possibile configurare il modo in cui le attività dell'app Novell. Android supportano la modalità a più finestre. È ad esempio possibile configurare gli attributi che impostano le dimensioni minime e l'altezza e la larghezza predefinite dell'app in modalità a più finestre. È possibile utilizzare la nuova proprietà `Activity.IsInMultiWindowMode` per determinare se l'attività è in modalità a più finestre. Ad esempio:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

L'app di esempio [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) C# include codice che illustra come sfruttare le interfacce utente di più finestre con l'app.

Per ulteriori informazioni sulla modalità a più finestre, vedere Supporto di più [finestre](https://developer.android.com/guide/topics/ui/multi-window.html).

### <a name="enhanced-notifications"></a>Notifiche avanzate

Android torrone introduce un sistema di notifica riprogettato. Include una nuova funzionalità di risposta diretta che consente agli utenti di rispondere rapidamente alle notifiche per i messaggi di testo in arrivo direttamente nell'interfaccia utente di notifica. A partire da Android 7,0, i messaggi di notifica possono essere raggruppati come un singolo gruppo quando viene ricevuto più di un messaggio. Inoltre, gli sviluppatori possono personalizzare le visualizzazioni delle notifiche, sfruttare le decorazioni di sistema nelle notifiche e sfruttare i nuovi modelli di notifica per la generazione di notifiche.

#### <a name="direct-reply"></a>Risposta diretta

Quando un utente riceve una notifica per il messaggio in arrivo, il torrone Android rende possibile rispondere al messaggio all'interno della notifica, anziché aprire l'app di messaggistica per inviare una risposta.
Questa funzionalità di risposta inline consente agli utenti di rispondere rapidamente a un SMS o a un messaggio di testo direttamente all'interno dell'interfaccia di notifica:

[![screenshot di una notifica con un campo di risposta diretta inline](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Per supportare questa funzionalità nell'app, è necessario aggiungere *azioni di risposta inline* all'app tramite un oggetto [RemoteInput](xref:Android.App.RemoteInput) in modo che gli utenti possano rispondere tramite testo direttamente dall'interfaccia utente di notifica.
Il codice seguente, ad esempio, compila un `RemoteInput` per la ricezione di input di testo, compila uno scopo in sospeso per l'azione di risposta e crea un'azione remota di input abilitata:

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

Questa azione viene aggiunta alla notifica:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

L'app di esempio del servizio C# di [messaggistica](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) include codice che illustra come estendere le notifiche con un oggetto `RemoteInput`. Per altre informazioni sull'aggiunta di azioni di risposta inline all'app per Android 7,0 o versione successiva, vedere l'argomento [risposta alle notifiche per](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) Android.

#### <a name="bundled-notifications"></a>Notifiche in bundle

Il torrone Android può raggruppare i messaggi di notifica (ad esempio, per argomento del messaggio) e visualizzare il gruppo anziché ogni messaggio separato.
Questa funzionalità per le *notifiche in bundle* consente agli utenti di ignorare o archiviare un gruppo di notifiche in un'unica azione. L'utente può scorrere verso il basso per espandere il bundle di notifiche per visualizzare ogni notifica in dettaglio:

[![screenshot di esempio di notifiche in bundle](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Per supportare le notifiche in bundle, l'app può usare il metodo [Builder. segroup](xref:Android.App.Notification.Builder.SetGroup*) per raggruppare le notifiche simili. Per ulteriori informazioni sui gruppi di notifiche in bundle in Android N, vedere l'argomento relativo alle [notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) per la creazione di bundle Android.

#### <a name="custom-views"></a>Viste personalizzate

Il torrone Android consente di creare visualizzazioni di notifiche personalizzate con le intestazioni, le azioni e i layout espandibili delle notifiche di sistema. Per altre informazioni sulle visualizzazioni di notifiche personalizzate in Android torrone, vedere l'argomento [miglioramenti delle notifiche](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) per Android.

### <a name="data-saver"></a>Salva dati

A partire da Android torrone, gli utenti possono abilitare una nuova impostazione di *risparmio di dati* che blocca l'utilizzo dei dati in background. Questa impostazione segnala anche all'app di usare un minor numero di dati in primo piano, laddove possibile. Il [ConnectivityManager](xref:Android.Net.ConnectivityManager) è stato esteso in Android torrone, in modo che l'app possa controllare se l'utente ha abilitato il risparmio di dati, in modo che l'app possa impegnarsi per limitare l'utilizzo dei dati quando è abilitato il risparmio di dati.

Per altre informazioni sulla nuova funzionalità di data saver in Android torrone, vedere l'argomento relativo all' [utilizzo dei dati di rete](https://developer.android.com/training/basics/network-ops/data-saver.html) per l'ottimizzazione di Android.

### <a name="app-shortcuts"></a>Collegamenti alle app

Android 7,1 ha introdotto una funzionalità di *collegamenti alle app* che consente agli utenti di avviare rapidamente le attività comuni o consigliate con l'app.
Per attivare il menu dei tasti di scelta rapida, l'utente preme a lungo l'icona dell'app per un secondo o più &ndash; il menu viene visualizzato con una vibrazione rapida.
Se si rilascia la pressione, il menu rimarrà:

[schermata di esempio ![di un menu di scelta rapida dell'app per un'app di messaggistica](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Questa funzionalità è disponibile solo per il livello API 25 o superiore.
Per altre informazioni sulla nuova funzionalità di collegamenti alle app in Android 7,1, vedere l'argomento [tasti di scelta rapida](https://developer.android.com/guide/topics/ui/shortcuts.html) per le app Android.

### <a name="sample-code"></a>Codice di esempio

Sono disponibili diversi esempi di Novell. Android per illustrare come sfruttare i vantaggi delle funzionalità torrone di Android:

- [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) illustra l'uso dell'API multifinestra disponibile nel torrone Android. È possibile passare l'app di esempio in modalità multiwindows per vedere come influiscono sul ciclo di vita e sul comportamento dell'app.

- Il [servizio messaggistica](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) è un servizio semplice che invia notifiche tramite il `NotificationCompatManager`. Estende inoltre la notifica con un oggetto `RemoteInput` per consentire ai dispositivi Android torrone di rispondere tramite testo direttamente dalla notifica senza dover aprire un'app.

- [Notifiche attive](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) illustra come usare l'API `NotificationManager` per indicare il numero di notifiche attualmente visualizzate dall'applicazione.

- [Accesso alla directory con ambito](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Viene illustrato come utilizzare l'API di accesso alla directory con ambito per accedere facilmente a directory specifiche. Questa funzione funge da alternativa alla necessità di definire `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazioni nel manifesto.

- [Avvio diretto](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Viene illustrato come archiviare i dati in una risorsa di archiviazione crittografata dal dispositivo, che è sempre disponibile quando il dispositivo viene avviato sia prima che dopo l'immissione delle credenziali utente (PIN/criterio/password).

## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto il torrone Android ed è stato illustrato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Novell. Android nel torrone Android. Fornisce inoltre una panoramica delle funzionalità principali disponibili in Android torrone, con collegamenti ad esempi di codice sorgente che consentono di iniziare a creare app per il torrone Android.

## <a name="related-links"></a>Collegamenti correlati

- [Android 7,1 per sviluppatori](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Note sulla versione di Novell Android 7,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
