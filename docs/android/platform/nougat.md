---
title: Funzionalità nougat
description: Come iniziare a usare xamarin per sviluppare App per Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: fe544f8ac677987f8921ccb1c11b8930811b9553
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="nougat-features"></a>Funzionalità nougat

_Come iniziare a usare xamarin per sviluppare App per Android Nougat._

Questo articolo fornisce una struttura delle funzionalità introdotte in Android Nougat, spiega come preparare xamarin per lo sviluppo di Android Nougat e vengono forniti collegamenti ad applicazioni di esempio che illustrano le funzionalità di Android Nougat App xamarin.


## <a name="overview"></a>Panoramica

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) è il completamento di Google per Android Marshmallow 6.0. Xamarin offre supporto per **Android 7. x associazioni** in Xamarin Android 7.0 e versioni successive. Android Nougat aggiunge molte nuove API per le funzionalità di Nougat descritte di seguito; Queste API sono disponibili per le app xamarin quando si usa xamarin 7.0.

[![Immagini hero di Tablet e telefoni Android Nougat esegue Android](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Per ulteriori informazioni su 7. x API Android, vedere [Android 7.1 per gli sviluppatori](http://developer.android.com/preview/api-overview.html).
Per un elenco di problemi noti di xamarin 7.0, vedere il [note sulla versione](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Android Nougat fornisce numerose nuove funzionalità di interesse per gli sviluppatori di xamarin. Queste funzionalità comprendono:

-   **Il supporto di più finestre** &ndash; questa funzionalità avanzata consente agli utenti di aprire contemporaneamente due App sullo schermo.

-   **Miglioramenti di notifica** &ndash; il sistema di notifiche riprogettato in Android Nougat include un *risposta diretta* funzionalità che consentono agli utenti di rispondere rapidamente ai messaggi di testo direttamente dalla notifica INTERFACCIA UTENTE. Inoltre, se l'applicazione crea le notifiche per ricevuti messaggi, il nuovo *collegate notifiche* funzionalità possibile raggruppare le notifiche insieme come un unico gruppo quando viene ricevuto più di un messaggio.

-   **Dati Saver** &ndash; questa funzionalità è un nuovo servizio di sistema che consente di ridurre l'uso di rete dati da app; consente agli utenti un controllo sulla rete dati di utilizzo delle app.

Inoltre, Nougat Android offre numerosi altri miglioramenti di particolare interesse per gli sviluppatori di applicazioni, ad esempio una nuova funzionalità di configurazione di sicurezza di rete, Doze in viaggio, la chiave di miglioramenti di WebView attestazione, new rapido impostazioni API, supporto di più impostazioni locali, API ICU4J, accesso alle funzionalità del linguaggio Java 8, accesso alla directory con ambito, un'API personalizzata di puntatore, supporto della piattaforma VR, file virtuali e le ottimizzazioni di elaborazione in background.

In questo articolo viene illustrato come iniziare a creare App con Android Nougat per provare le nuove funzionalità e pianificare la migrazione o la funzionalità lavoro per la nuova piattaforma Nougat Android.


## <a name="requirements"></a>Requisiti

Di seguito è necessario utilizzare le nuove funzionalità di Android Nougat nelle App basate su Xamarin:

-   **Visual Studio o Visual Studio per Mac** &ndash; se si utilizza Visual Studio, versione 4.2.0.628 o versioni successive di Xamarin per Visual Studio è obbligatoria. Se si utilizza Visual Studio per Mac, versione 6.1.0 o versione successiva di Visual Studio per Mac è necessario.

-   **Xamarin** &ndash; xamarin 7.0 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-   **Android SDK** -Android SDK 7.0 (API 24) o versioni successive deve essere installato mediante Android SDK Manager.

-   **Java Developer Kit** &ndash; lo sviluppo con Xamarin Android 7.0 richiede [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se sviluppano applicazioni per il livello di API 24 o superiore (JDK 8 supporta inoltre API livelli precedenti a 24). La versione a 64 bit di JDK 8 è obbligatoria se si utilizza il Visualizzatore di form o di controlli personalizzati.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

Si noti che le app devono essere ricompilate con Xamarin C6SR4 o versione successiva per funzionare in modo affidabile con Nougat Android. Poiché Nougat Android è possibile collegare solo a [librerie native fornite NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), le applicazioni esistenti utilizzando le librerie, ad esempio **Mono.Data.Sqlite.dll** potrebbe bloccarsi durante l'esecuzione su Android Nougat se non sono correttamente ricompilato.



## <a name="getting-started"></a>Introduzione

Per iniziare a utilizzare Nougat Android con xamarin, è necessario scaricare e installare il più recente degli strumenti e i pacchetti SDK prima di poter creare un progetto Android Nougat:

1.  Installare gli ultimi aggiornamenti di xamarin da di Xamarin.

2.  Installare il **Android 7.0 (API 24)** pacchetti e gli strumenti o versione successiva.

3.  Creare un nuovo progetto xamarin destinata Nougat Android.

4.  Configurare un dispositivo o l'emulatore per Android Nougat.

Ognuno di questi passaggi verrà illustrato nelle sezioni seguenti:


### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Xamarin

Per aggiungere il supporto di Xamarin per Android Nougat, modificare il canale aggiornamenti in Visual Studio o Visual Studio per Mac per il canale stabile e applicare gli aggiornamenti più recenti. Se è necessario anche le funzionalità attualmente disponibile solo nel canale alfa o Beta, è possibile passare al canale alfa o Beta (i canali alfa e Beta forniscono inoltre supporto per Android 7. x). Per informazioni su come modificare il canale aggiornamenti (versioni), vedere [modifica il canale aggiornamenti](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/).



### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con Xamarin Android 7.0, è necessario utilizzare innanzitutto Android SDK Manager per installare **N di Android SDK Platform (API 24)** o versione successiva. È inoltre necessario installare la versione più recente **strumenti Android SDK**:

1.  Avviare Android SDK Manager (in Visual Studio per Mac, usare **strumenti > Apri Android SDK Manager&hellip;**; in Visual Studio, usare **strumenti > Android > Android SDK Manager**).

2.  Installare **Android 7.0 (API 24)** o versione successiva:

    [![Selezionare i pacchetti Android 7.0 in Android SDK Manager](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Installare gli strumenti di Android SDK più recenti:

    [![Selezionare gli strumenti di Android SDK più recenti di Android SDK Manager](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    È necessario installare revisione strumenti Android SDK 25.2.2 o versioni successive, Android Platform SDK strumenti 24.0.3 o versione successiva, quindi Android SDK Build 24.0.2 o versione successiva.

4.  Verificare che il **Java Development Kit percorso** è configurato per JDK 1.8:

    [![Il percorso di JDK 8 nelle opzioni del menu Strumenti di configurazione](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Per visualizzare questa impostazione in Visual Studio, fare clic su **strumenti > Opzioni > Xamarin > Impostazioni Android**. In Visual Studio per Mac, fare clic su **Preferenze > progetti > percorsi SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin

Creare un nuovo progetto di xamarin. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per informazioni sulla creazione di progetti di xamarin.

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione di destinazione Android 7.0 o versione successiva. Ad esempio, per la destinazione del progetto per Android 7.0, è necessario configurare il livello API Android di destinazione del progetto per **Android 7.0 (API 24 - Nougat)**. È consigliabile impostare il livello di framework di destinazione a 24 API o versioni successive. Per ulteriori informazioni sulla configurazione dei livelli di livello API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> Attualmente è necessario impostare il **minimo Android versione** a **Android 7.0 (API 24 - Nougat)** per distribuire l'app per dispositivi Android Nougat o gli emulatori.



### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si utilizza un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo usando le impostazioni seguenti:

-   Dispositivo: Nexus 5 X, Nexus 6, Nexus P 6, Windows Media Player Nexus, Nexus 9 o Pixel C.
-   Destinazione: Android 7.0 - livello API 24
-   ABI: x86 o x86\_64

Ad esempio, questo dispositivo virtuale è configurato per emulare un 6 Nexus:

[![Configurazione di un AVD utilizzando dispositivo Nexus 6, destinazione Android 7.0 e CPU/ABI Intel Atom x86](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se si utilizza un dispositivo fisico, ad esempio un nodo X 5, 6 o 9, è possibile aggiornare il dispositivo tramite automatico sugli aggiornamenti air (OTA) o scaricare un'immagine del sistema e flash direttamente il dispositivo. Per ulteriori informazioni sull'aggiornamento manuale del dispositivo per Nougat Android, vedere [OTA immagini per i dispositivi Nexus](https://developers.google.com/android/nexus/ota).

Si noti che i dispositivi Nexus 5 non sono supportati da Android Nougat.



## <a name="new-features"></a>Nuove funzionalità

Android Nougat introduce varie nuove caratteristiche e funzionalità, ad esempio il supporto di più finestre, miglioramenti di notifiche e Screen Saver di dati. Nelle sezioni seguenti evidenziano queste funzionalità e forniscono i collegamenti che consentono di iniziare a usarli nell'app.



### <a name="multi-window-mode"></a>Modalità multi-finestra

La modalità multi-finestra consente agli utenti di aprire contemporaneamente due App con supporto per il multitasking completo. Queste App possono essere eseguite in modalità a schermo diviso side-by-side (orizzontale) o uno-sopra-the-other (verticale).
Gli utenti possono trascinare un divisore tra le App per ridimensionarle e possono tagliare e incollare il contenuto di tra app. Quando due applicazioni sono presentate in modalità multi-finestra, l'attività selezionata continua a eseguire mentre l'attività non selezionata non è sospesa, ma rimane visibile. Modalità multi-finestra non di modificare il ciclo di vita di attività Android.

[![Applicazioni di esempio in esecuzione in modalità multi-finestra in verticale e orizzontale](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

È possibile configurare come le attività dell'app xamarin supportano la modalità più finestre. Ad esempio, è possibile configurare gli attributi che imposta la dimensione minima e l'altezza e larghezza predefinite dell'app in modalità multi. È possibile utilizzare il nuovo `Activity.IsInMultiWindowMode` proprietà per determinare se l'attività è in modalità multi. Ad esempio:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

Il [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) app di esempio include il codice c# che illustra come sfruttare i vantaggi della finestra più interfacce utente con l'app.

Per ulteriori informazioni sulla multi-finestra modalità, vedere il [il supporto di più finestre](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notifiche avanzate

Android Nougat introduce un sistema di notifica riprogettato. È dotato di una nuova funzionalità di risposta diretta che rende possibile per gli utenti di rispondere rapidamente alle notifiche per i messaggi di testo in ingresso direttamente nella notifica dell'interfaccia utente. A partire da Android 7.0, i messaggi possono raggruppare insieme come un singolo gruppo quando viene ricevuto più di un messaggio di notifica. Inoltre, gli sviluppatori possono personalizzare notifica sfruttare decorazioni di sistema nelle notifiche, visualizzazioni e sfruttare i vantaggi dei nuovi modelli di notifica durante la generazione di notifiche.


#### <a name="direct-reply"></a>Risposta diretta

Quando un utente riceve una notifica di messaggio in ingresso, Nougat Android rende possibile per rispondere al messaggio all'interno della notifica (anziché aprire l'app invia una risposta di messaggistica).
Questa funzionalità di risposta inline consente agli utenti di rispondere rapidamente a un messaggio SMS o testo direttamente all'interno dell'interfaccia di notifica:

[![Schermata di una notifica con un campo di risposta diretta inline](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Per supportare questa funzionalità nell'app, è necessario aggiungere *azioni di risposta inline* all'App tramite un [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) in modo che gli utenti possono rispondere tramite testo direttamente dalla notifica dell'interfaccia utente dell'oggetto.
Ad esempio, il codice seguente compilazioni un `RemoteInput` per la ricezione di input di testo, compila una finalità in sospeso per l'azione di risposta e crea un'azione di input abilitata remota:

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

Questa azione viene aggiunto alla notifica:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

Il [il servizio di messaggistica](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) app di esempio include il codice c# che illustra come estendere le notifiche con un `RemoteInput` oggetto. Per ulteriori informazioni sull'aggiunta di risposta inline azioni per l'app per Android 7.0 o versione successiva, vedere il Android [risposta a notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) argomento.


#### <a name="bundled-notifications"></a>Notifiche in dotazione

Nougat Android è possibile raggruppare i messaggi di notifica (ad esempio, per l'argomento di messaggio) e visualizzare il gruppo al posto di ogni messaggio separato.
Questo *collegate notifiche* funzionalità rende possibile per gli utenti di eliminare o archiviare un gruppo di notifiche in un'azione. L'utente può scorrere verso il basso per espandere il bundle di visualizzare ogni notifica in modo dettagliato le notifiche:

[![Schermata di esempio di notifiche in dotazione](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Per supportare le notifiche in dotazione, è possibile utilizzare l'app di [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) metodo per aggregare le notifiche simili. Per ulteriori informazioni sui gruppi di notifica in dotazione in N Android, vedere il Android [aggregazione notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) argomento.


#### <a name="custom-views"></a>Viste personalizzate

Android Nougat rende possibile la creazione di viste di notifica personalizzata con intestazioni di notifica di sistema, azioni e layout espandibile. Per ulteriori informazioni sulle visualizzazioni di notifica personalizzata in Nougat Android, vedere il Android [notifica miglioramenti](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) argomento.



### <a name="data-saver"></a>Screen Saver di dati

A partire da Android Nougat, gli utenti possono attivare un nuovo *dati Saver* impostazione che blocchi di utilizzo dei dati in background. Questa impostazione Segnala anche all'app di usare meno dati in primo piano, laddove possibile. Il [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) è stato esteso in Android Nougat in modo che l'app è possibile verificare se l'utente ha abilitato dati Saver in modo che l'app possa allo scopo di limitare l'utilizzo di dati quando è abilitato Screen Saver di dati.

Per ulteriori informazioni sulla nuova funzionalità in Android Nougat Saver di dati, vedere il Android [ottimizzazione dell'utilizzo di dati di rete](https://developer.android.com/training/basics/network-ops/data-saver.html) argomento.



### <a name="app-shortcuts"></a>Tasti di scelta rapida App

7.1 Android introdotto un *collegamenti App* funzionalità che consente agli utenti di rapidamente inizio consigliate le attività comuni o con l'app.
Per attivare il menu di scelta rapida, l'utente long-pressioni l'icona dell'app per un secondo o più &ndash; viene visualizzato il menu con una vibrazione rapida.
Rilasciare la pressione di conseguenza, il menu di rimanere:

[![Schermata di esempio di un menu di scelta rapida di app per un'applicazione di messaggistica](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Questa funzionalità è disponibile solo livello di API 25 o superiore.
Per ulteriori informazioni sulla nuova funzionalità di scelta rapida di App in Android 7.1, vedere il Android [collegamenti App](https://developer.android.com/guide/topics/ui/shortcuts.html) argomento.


### <a name="sample-code"></a>Codice di esempio

Alcuni esempi di xamarin sono disponibili per viene illustrato come sfruttare le funzionalità di Android Nougat:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) viene illustrato l'utilizzo dell'API disponibile in Android Nougat più finestre. È possibile passare l'app di esempio in modalità multi-windows per osservare gli effetti ciclo di vita e il comportamento dell'app.

-   [Servizio di messaggistica](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) utilizza un servizio semplice che invia le notifiche di `NotificationCompatManager`. Estende la notifica con un `RemoteInput` oggetto per consentire ai dispositivi Android Nougat risposta tramite testo direttamente dalla notifica, senza dover aprire un'app.

-   [Attive notifiche](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) viene illustrato come utilizzare il `NotificationManager` API per indicare il numero di notifiche dell'applicazione è attualmente visualizzato.

-   [Ambito di accesso alla Directory](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) di seguito viene illustrato come utilizzare l'accesso alla directory con ambito API per accedere facilmente a directory specifiche. Questo costituisce un'alternativa a dover definire `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazioni nel manifesto.

-   [Avvio di indirizzare](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) viene illustrato come archiviare i dati in un archivio crittografato dispositivo che è sempre disponibile mentre il dispositivo è stato avviato entrambi prima e dopo aver immesso credentials(PIN/Pattern/Password) qualsiasi utente.


## <a name="summary"></a>Riepilogo

In questo articolo introdotto Nougat Android e viene illustrato come installare e configurare gli strumenti di più recenti e i pacchetti per lo sviluppo di xamarin in Android Nougat. Inoltre, fornisce una panoramica delle funzionalità chiave disponibili in Android Nougat, con collegamenti a codice sorgente di esempio che consentono di iniziare la creazione di App per Android Nougat.


## <a name="related-links"></a>Collegamenti correlati

- [7.1 Android per gli sviluppatori](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Note sulla versione di Xamarin Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
