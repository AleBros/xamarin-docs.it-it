---
title: Funzionalità nougat
description: Come iniziare a usare xamarin. Android per sviluppare App per Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 2e15de944f05fede802dbf52987d80a46fb890ef
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242153"
---
# <a name="nougat-features"></a>Funzionalità nougat

_Come iniziare a usare xamarin. Android per sviluppare App per Android Nougat._

Questo articolo fornisce una descrizione delle funzionalità introdotte in Android Nougat, spiega come preparare xamarin. Android per lo sviluppo di Android Nougat e vengono forniti collegamenti ad applicazioni di esempio che illustrano come usare funzionalità di Android Nougat App xamarin. Android.


## <a name="overview"></a>Panoramica

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) è completamento di Google per Android Marshmallow 6.0. Xamarin. Android fornisce il supporto per **Android 7.x associazioni** in Xamarin Android 7.0 e versioni successive. Android Nougat aggiunge molte nuove API per le funzionalità di Nougat descritte di seguito; Queste API sono disponibili per le app xamarin. Android quando si usa xamarin. Android 7.0.

[![Immagini di logo alto dei Tablet e telefoni che eseguono Android Nougat Android](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Per altre informazioni su Android 7.x API, vedere [Android 7.1 per gli sviluppatori](http://developer.android.com/preview/api-overview.html).
Per un elenco di problemi noti di xamarin. Android 7.0, vedere la [note sulla versione](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Android Nougat offre molte nuove funzionalità di interesse per gli sviluppatori di xamarin. Android. Queste funzionalità comprendono:

-   **Il supporto di più finestre** &ndash; questa funzionalità avanzata consente agli utenti di aprire contemporaneamente due App sullo schermo.

-   **Miglioramenti di notifica** &ndash; il sistema di notifiche riprogettata in Android Nougat include un *risposta diretta* funzionalità che consentono agli utenti di rispondere rapidamente ai messaggi di testo direttamente dalla notifica INTERFACCIA UTENTE. Inoltre, se l'app crea le notifiche per ricevuto i messaggi, il nuovo *abbinato notifiche* funzionalità possibile riunire le notifiche elementi in un singolo gruppo quando viene ricevuto più di un messaggio.

-   **Dati Saver** &ndash; questa funzionalità è un nuovo servizio di sistema che consente di ridurre l'uso di dati cellulare, le app, offre agli utenti di controllo sul modo in cui le app usano dati cellulari.

Inoltre, Android Nougat comporta numerosi altri miglioramenti di interesse per gli sviluppatori di app, ad esempio una nuova funzionalità di configurazione di sicurezza di rete, Doze in viaggio, miglioramenti per l'attestazione, nuove API impostazioni rapide, supporto multi-impostazioni locali, le API ICU4J, WebView, della chiave accesso alle funzionalità del linguaggio Java 8, accesso alla directory con ambito, un'API personalizzata puntatore, supporto della piattaforma di realtà virtuale, file virtuali e le ottimizzazioni di elaborazione in background.

Questo articolo illustra come iniziare a creare App con Android Nougat per provare le nuove funzionalità e pianificare la migrazione o la funzionalità lavoro per la nuova piattaforma Android Nougat di destinazione.


## <a name="requirements"></a>Requisiti

Per utilizzare le nuove funzionalità di Android Nougat nelle App basate su Xamarin è necessario quanto segue:

-   **Visual Studio o Visual Studio per Mac** &ndash; se si usa Visual Studio, versione 4.2.0.628 o successiva di Visual Studio Tools per Xamarin è obbligatorio. Se si usa Visual Studio per Mac, versione 6.1.0 o versione successiva di Visual Studio per Mac è obbligatorio.

-   **Xamarin. Android** &ndash; xamarin. Android 7.0 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-   **Android SDK** -Android 7.0 SDK (API 24) o versione successiva deve essere installato tramite Android SDK Manager.

-   **Java Developer Kit** &ndash; richiede lo sviluppo di Xamarin Android 7.0 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sviluppa per il livello API 24 o versione successiva (JDK 8 supporta anche i livelli API precedenti a 24). Se si usa il Visualizzatore anteprima del form o controlli personalizzati, è necessaria la versione a 64 bit di JDK 8.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

Si noti che le app devono essere ricompilate con Xamarin C6SR4 o versione successiva per funzionare in modo affidabile con Android Nougat. Poiché Android Nougat può collegarsi solo a [fornite NDK librerie native](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), usando le librerie, ad esempio le app esistenti **Mono.Data.Sqlite.dll** potrebbe bloccarsi durante l'esecuzione in Android Nougat se non vengono correttamente ricompilato.



## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android Nougat con xamarin. Android, è necessario scaricare e installare gli strumenti più recenti e i pacchetti SDK prima di poter creare un progetto Android Nougat:

1.  Installare gli ultimi aggiornamenti di xamarin. Android da di Xamarin.

2.  Installare il **Android 7.0 (API 24)** pacchetti e gli strumenti o versione successiva.

3.  Creare un nuovo progetto xamarin. Android destinata a Android Nougat.

4.  Configurare un dispositivo o emulatore per Android Nougat.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:


### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Xamarin

Per aggiungere il supporto di Xamarin per Android Nougat, modificare il canale aggiornamenti in Visual Studio o Visual Studio per Mac per il canale stabile e applicare gli aggiornamenti più recenti. Se è necessario anche le funzionalità attualmente disponibili solo nel canale alfa o Beta, è possibile passare al canale alfa o Beta (i canali alfa e Beta garantiscono inoltre supporto per Android 7.x). Per informazioni su come modificare il canale aggiornamenti (versioni), vedere [modificando il canale aggiornamenti](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).



### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con Xamarin Android 7.0, è necessario prima di tutto usare Android SDK Manager per installare **SDK della piattaforma Android N (API 24)** o versione successiva. È inoltre necessario installare la versione più recente **Android SDK Tools**:

1.  Avviare Android SDK Manager (in Visual Studio per Mac, usare **strumenti > Apri Android SDK Manager&hellip;**; in Visual Studio, utilizzano **strumenti > Android > Android SDK Manager**).

2.  Installare **Android 7.0 (API 24)** o versione successiva:

    [![Selezionare i pacchetti Android 7.0 in Android SDK Manager](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Installare gli strumenti di Android SDK più recenti:

    [![Selezionare gli strumenti di Android SDK più recenti in Android SDK Manager](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    È necessario installare Android SDK Tools revisione 25.2.2 o versioni successive, Android strumenti piattaforma SDK 24.0.3 o versioni successive e Android SDK Build tools 24.0.2 o versione successiva.

4.  Verificare che il **percorso di Java Development Kit** è configurato per JDK 1.8:

    [![Configurare il percorso del JDK 8 in opzioni del menu Strumenti](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Per visualizzare questa impostazione in Visual Studio, fare clic su **strumenti > Opzioni > Xamarin > Impostazioni Android**. In Visual Studio per Mac, fare clic su **Preferenze > progetti > percorsi SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin. Android

Creare un nuovo progetto xamarin. Android. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti xamarin. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni di versione di destinazione Android 7.0 o versione successiva. Ad esempio, per impostare come destinazione del progetto per Android 7.0, è necessario configurare il livello API Android di destinazione del progetto per **Android 7.0 (API 24 - Nougat)**. È consigliabile impostare il livello di framework di destinazione e API 24 o versioni successive. Per altre informazioni sulla configurazione dei livelli a livello API Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> Attualmente è necessario impostare il **Minimum Android version** al **Android 7.0 (API 24 - Nougat)** per distribuire l'app per Android Nougat dispositivi o emulatori.



### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si usa un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo usando le impostazioni seguenti:

-   Dispositivo: Nexus 5 X, Nexus 6 Nexus 6P, del lettore Nexus Nexus 9 o Pixel C.
-   Destinazione: Android 7.0 - livello API 24
-   : ABI x86 o x86\_64

Ad esempio, il dispositivo virtuale è configurato per emulare un Nexus 6:

[![Configurazione di un dispositivo virtuale Android usando dispositivi Nexus 6, destinazione di Android 7.0 e CPU/ABI Atom di Intel x86](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se si usa un dispositivo fisico, ad esempio un Nexus 5 X, 6 o 9, è possibile aggiornare il dispositivo tramite automatico tramite gli aggiornamenti di air (OTA) o scaricare un'immagine del sistema e flash direttamente il dispositivo. Per altre informazioni sull'aggiornamento manuale del dispositivo per Android Nougat, vedere [OTA immagini per i dispositivi Nexus](https://developers.google.com/android/nexus/ota).

Si noti che i dispositivi Nexus 5 non sono supportati da Android Nougat.



## <a name="new-features"></a>Nuove funzionalità

Android Nougat introduce un'ampia gamma di nuove caratteristiche e funzionalità, quali il supporto di più finestre, i miglioramenti delle notifiche e Screen Saver di dati. Le sezioni seguenti vengono evidenziate queste funzionalità e forniscono i collegamenti che consentono di iniziare a usarle nell'app.



### <a name="multi-window-mode"></a>Modalità multi-finestra

La modalità multi-finestra rende possibile per gli utenti di aprire contemporaneamente due App con supporto completo multitasking. Queste App possono eseguire side-by-side (orizzontale) o uno-sopra-the-other (verticale) in modalità a schermo diviso.
Gli utenti potranno trascinare un divisore tra le App per ridimensionarle e possono tagliare e incollare il contenuto di tra app. Quando vengono presentate due App in modalità a più finestre, l'attività selezionata resta in esecuzione mentre l'attività non selezionato è sospesa, ma rimane visibile. La modalità multi-finestra non modifica il ciclo di vita di attività Android.

[![App di esempio in esecuzione in modalità a più finestre in verticale e orizzontale](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

È possibile configurare come le attività dell'app xamarin. Android supportano la modalità a più finestre. Ad esempio, è possibile configurare gli attributi che impostare la dimensione minima e l'altezza predefinita e la larghezza dell'app in modalità a più finestre. È possibile usare il nuovo `Activity.IsInMultiWindowMode` proprietà per determinare se l'attività è in modalità a più finestre. Ad esempio:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

Il [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) app di esempio include codice c# che illustra come sfruttare i vantaggi della finestra più interfacce utente con l'app.

Per altre informazioni sulla modalità a più finestre, vedere la [il supporto di più finestre](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notifiche migliorate

Android Nougat introduce un sistema di notifica completamente rinnovata. È dotato di una nuova funzionalità di risposta diretta che rende possibile per gli utenti di rispondere rapidamente alle notifiche per i messaggi di testo in ingresso direttamente nella notifica dell'interfaccia utente. A partire da Android 7.0, i messaggi possono essere collegati tra loro come un unico gruppo quando viene ricevuto più di un messaggio di notifica. Inoltre, gli sviluppatori possono personalizzare notifica viste, sfruttare le decorazioni di sistema nelle notifiche e sfruttare i vantaggi di nuovi modelli di notifica durante la generazione di notifiche.


#### <a name="direct-reply"></a>Risposta diretta

Quando un utente riceve una notifica di messaggio in ingresso, Android Nougat rende possibile rispondere al messaggio all'interno della notifica (anziché aprire l'app di messaggistica per inviare un messaggio di risposta).
Questa funzionalità di risposta inline rende possibile per gli utenti di rispondere tempestivamente a un SMS o messaggio di testo direttamente all'interno dell'interfaccia di notifica:

[![Screenshot di una notifica con un campo di risposta diretta inline](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Per supportare questa funzionalità nell'app, è necessario aggiungere *azioni di risposta inline* all'App tramite un [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) dell'oggetto in modo che gli utenti possono rispondere tramite testo direttamente dalla notifica dell'interfaccia utente.
Ad esempio, il codice seguente le compilazioni un `RemoteInput` per la ricezione di input di testo, compila un programma in sospeso per l'azione di risposta e crea un'azione abilitata input remota:

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

Questa azione viene aggiunto a notifica:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

Il [il servizio di messaggistica](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) app di esempio include codice c# che illustra come estendere le notifiche con un `RemoteInput` oggetto. Per altre informazioni sull'aggiunta di risposta inline azioni per l'app per Android 7.0 o versioni successive, vedere l'Android [rispondere alle notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) argomento.


#### <a name="bundled-notifications"></a>Notifiche in bundle

Android Nougat possibile raggruppare i messaggi di notifica (ad esempio, per l'argomento del messaggio) e visualizzare il gruppo piuttosto che ogni messaggio separato.
Ciò *abbinato notifiche* funzionalità rende possibile per gli utenti di eliminare o archiviare un gruppo di notifiche in un'unica azione. L'utente può scorrere verso il basso per espandere il bundle di notifiche per visualizzare ogni notifica in modo dettagliato:

[![Screenshot di esempio di notifiche in bundle](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Per supportare le notifiche in bundle, l'app può usare la [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) metodo per creare un bundle simile notifiche. Per altre informazioni sui gruppi di notifica in bundle in Android N, vedere l'Android [creazione di bundle notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) argomento.


#### <a name="custom-views"></a>Viste personalizzate

Android Nougat rende possibile creare viste personalizzate di notifica con layout espandibile, azioni e le intestazioni di notifica di sistema. Per altre informazioni sulle viste di notifica personalizzato in Android Nougat, vedere l'Android [miglioramenti della notifica](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) argomento.



### <a name="data-saver"></a>Screen Saver di dati

A partire da Android Nougat, gli utenti possono abilitare una nuova *Saver dati* impostazione che blocchi sfondo sull'utilizzo dei dati. Questa impostazione Segnala anche all'app di usare meno dati in primo piano, laddove possibile. Il [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) è stato esteso in Android Nougat in modo che l'app può controllare se l'utente ha abilitato dati Saver in modo che l'app può effettuare un impegno per limitare l'utilizzo di dati quando è abilitato Screen Saver di dati.

Per altre informazioni sulla nuova funzionalità in Android Nougat Screen Saver di dati, vedere l'Android [ottimizzazione della rete dati utilizzo](https://developer.android.com/training/basics/network-ops/data-saver.html) argomento.



### <a name="app-shortcuts"></a>Tasti di scelta rapida App

Android 7.1 introdotta un' *tasti di scelta rapida App* caratteristica che rende possibile per gli utenti rapidamente comuni o consigliata le attività di avvio con l'app.
Per attivare il menu di scelta rapida, l'utente prolungata-pressioni sull'icona dell'app per un secondo o più &ndash; viene visualizzato il menu con un rapido esempio sulle vibrazioni.
Rilasciare la pressione fa sì che il menu deve rimanere:

[![Schermata di esempio di un menu di scelta rapida di app per un'app di messaggistica](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Questa funzionalità è disponibile solo a livello API 25 o versione successiva.
Per altre informazioni sulla nuova funzionalità di scelta rapida di App in Android 7.1, vedere l'Android [tasti di scelta rapida App](https://developer.android.com/guide/topics/ui/shortcuts.html) argomento.


### <a name="sample-code"></a>Codice di esempio

Alcuni esempi di xamarin. Android sono disponibili per mostrarvi come sfruttare i vantaggi delle funzionalità di Android Nougat:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) illustra l'uso dell'API disponibile in Android Nougat a più finestre. È possibile passare l'app di esempio in modalità multi-windows per osservare gli effetti ciclo di vita e il comportamento dell'app.

-   [Servizio di messaggistica](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) è un servizio semplice che invii notifiche tramite il `NotificationCompatManager`. Estende anche la notifica con un `RemoteInput` oggetto per consentire ai dispositivi Android Nougat a cui rispondere tramite testo direttamente dalla notifica senza dover aprire un'app.

-   [Attive notifiche](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) illustra come usare il `NotificationManager` API per indicare il numero di notifiche è attualmente visualizzato o l'applicazione.

-   [Nell'ambito di accesso alla Directory](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) viene illustrato come usare l'API di accesso con ambito di directory per accedere facilmente alle directory specifiche. Viene usato come alternativa alla necessità di definire `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazioni nel manifesto.

-   [Indirizzare avvio](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) viene illustrato come archiviare i dati in un archivio crittografato al dispositivo che è sempre disponibile anche se il dispositivo è stato avviato entrambi prima e dopo aver immesso i credentials(PIN/Pattern/Password) qualsiasi utente.


## <a name="summary"></a>Riepilogo

Questo articolo introdotto Android Nougat ed è stato spiegato come installare e configurare gli strumenti e i pacchetti per lo sviluppo di xamarin. Android più recenti in Android Nougat. Inoltre fornita una panoramica delle funzionalità chiave disponibili in Android Nougat, con collegamenti a codice sorgente di esempio che consentono di iniziare la creazione di App per Android Nougat.


## <a name="related-links"></a>Collegamenti correlati

- [Android 7.1 per gli sviluppatori](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Note sulla versione di Xamarin. Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
