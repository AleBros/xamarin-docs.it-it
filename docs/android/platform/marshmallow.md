---
title: Funzionalità marshmallow
description: Questo articolo è utile per iniziare a usare Novell. Android per sviluppare app per Android 6,0 marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: fb1ba92be9527d490b3d34bd4c0e454b0a750837
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019982"
---
# <a name="marshmallow-features"></a>Funzionalità marshmallow

_Questo articolo è utile per iniziare a usare Novell. Android per sviluppare app per Android 6,0 marshmallow._

Questo articolo fornisce una descrizione delle nuove funzionalità di Android 6,0 marshmallow, spiega come preparare Novell. Android per lo sviluppo di marshmallow Android e fornisce collegamenti ad applicazioni di esempio che illustrano come usare il nuovo Android marshmallow funzionalità delle app Novell. Android. 

## <a name="overview"></a>Panoramica

[Android 6,0 marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), la prossima versione principale di Android dopo Android Lollipop.
Novell. Android supporta i dispositivi Android marshmallow e include:

- **Binding API 23/android 6,0** &ndash; Android 6,0 aggiunge molte nuove API per le nuove funzionalità descritte di seguito. Queste API sono disponibili per le app Novell. Android quando si usa il livello API 23 di destinazione. Per altre informazioni sulle API Android 6,0, vedere [API android 6,0](https://developer.android.com/preview/api-overview.html). 

[![immagini Hero di Tablet e telefoni che eseguono marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Sebbene la versione marshmallow sia principalmente dedicata alla "Polonia e alla qualità", offre anche molte nuove funzionalità interessanti per gli sviluppatori Novell. Android. Queste funzionalità comprendono: 

- Le **autorizzazioni di Runtime** &ndash; questa funzionalità avanzata consentono agli utenti di approvare le autorizzazioni di sicurezza in base alla situazione in fase di esecuzione. 

- **Miglioramenti dell'autenticazione** &ndash; a partire da marshmallow Android, le app possono ora usare i sensori di impronta digitale per autenticare gli utenti e una nuova funzionalità di *conferma delle credenziali* riduce al minimo la necessità di immettere le password. 

- Il **collegamento di app** &ndash; questa funzionalità consente di eliminare la necessità di visualizzare la selezione delle **app** associando automaticamente le app ai domini Web. 

- **Condivisione diretta** &ndash; è possibile definire *destinazioni* di condivisione diretta che rendono la condivisione rapida e intuitiva per gli utenti. Questa funzionalità consente a uers di condividere il contenuto con altre app. 

- **Interazioni vocali** &ndash; questa nuova API consente di creare funzionalità vocali in conversazione nell'app. 

- **modalità di visualizzazione 4k** &ndash; in Android marshmallow, l'app può richiedere una risoluzione dello schermo 4K sull'hardware che la supporta. 

- **Nuove funzionalità Audio** &ndash; a partire da marshmallow, Android supporta ora il protocollo MIDI. Fornisce inoltre nuove classi per la creazione di oggetti digitali per l'acquisizione e la riproduzione di audio e offre nuovi hook API per l'associazione di dispositivi audio e di input. 

- **Nuove funzionalità Video** &ndash; marshmallow fornisce una nuova classe che consente alle app di eseguire il rendering di flussi audio e video sincronizzati. Questa classe fornisce anche il supporto per la velocità di riproduzione dinamica. 

- **Android for Work** &ndash; marshmallow include controlli avanzati per i dispositivi di proprietà dell'azienda e utenti singoli. Supporta l'installazione e la disinstallazione invisibile all'utente del proprietario del dispositivo, l'accettazione automatica degli aggiornamenti del sistema, la gestione dei certificati migliorata, il monitoraggio dell'utilizzo dei dati, la gestione delle autorizzazioni e le notifiche dello stato del lavoro. 

- La libreria di supporto per la **progettazione di materiali** &ndash; la nuova libreria di *supporto* per la progettazione offre componenti e modelli di progettazione che semplificano la creazione di un aspetto di progettazione del materiale nell'app. 

Sono stati inoltre rilasciati molti aggiornamenti della libreria Android core con Android M e questi aggiornamenti forniscono nuove funzionalità per Android M e per le versioni precedenti di Android.

Sono stati inoltre rilasciati molti aggiornamenti della libreria Android core con Android marshmallow e questi aggiornamenti forniscono nuove funzionalità per Android marshmallow e per le versioni precedenti di Android. Questo articolo illustra come iniziare a creare app con Android marshmallow e fornisce una panoramica delle nuove funzionalità in Android 6,0. 

## <a name="requirements"></a>Requisiti

Di seguito sono riportate le funzionalità necessarie per usare le nuove funzionalità di Android marshmallow nelle app basate su Novell: 

- **Novell. android** &ndash; Novell. Android 5.1.7.12 o versione successiva deve essere installato e configurato con Visual Studio o Xamarin Studio.

- **Visual Studio per Mac** o **Visual Studio** &ndash; se si usa Visual Studio per Mac, è richiesta la versione 5.9.7.22 o successiva. Se si usa Visual Studio, è necessaria la versione 3.11.1537 o successiva di Novell Tools per Visual Studio. 

- **Android SDK** &ndash; Android SDK 6,0 (API 23) o versione successiva deve essere installato tramite il Android SDK Manager.

- **Java Developer Kit** &ndash; Novell. Android richiede [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sta sviluppando per il livello API 24 o versione successiva (JDK 1,8 supporta anche i livelli API precedenti a 24, incluso marshmallow). La versione a 64 bit di JDK 1,8 è obbligatoria se si usano controlli personalizzati o il Visualizzatore anteprima moduli.

È possibile continuare a usare [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si sta sviluppando in modo specifico per il livello API 23 o versioni precedenti. 

## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android marshmallow con Novell. Android, è necessario scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare un progetto Android marshmallow: 

1. Installare gli aggiornamenti più recenti di Novell dal canale **stabile** . 

2. Installare gli strumenti e i pacchetti di Android 6,0 marshmallow SDK.

3. Creare un nuovo progetto Novell. Android destinato a Android 6,0 marshmallow (livello API 23). 

4. Configurare un emulatore o un dispositivo per Android marshmallow.

Ognuno di questi passaggi è descritto nelle sezioni seguenti:

### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Novell

Per aggiornare Novell in modo che includa il supporto per Android 6,0 marshmallow, modificare il canale di aggiornamento in **stabile** e installare tutti gli aggiornamenti. Per ulteriori informazioni sull'installazione degli aggiornamenti dal canale degli aggiornamenti, vedere [Change the Updates Channel](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Installare Android 6,0 SDK

Per creare un progetto Novell. Android per marshmallow Android, è necessario prima di tutto usare gestione Android SDK per installare Android 6,0 SDK:

- Avviare Gestione Android SDK (in Visual Studio per Mac usare **gli strumenti > SDK Manager**. in Visual Studio usare **gli strumenti > Android > Android SDK Manager**) e installare la versione più recente di Android SDK Tools:

    [![selezione degli strumenti Android SDK nel gestore Android SDK](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Installare anche i pacchetti più recenti di **Android 6,0** SDK:

    [![selezione dei pacchetti SDK per Android 6,0 in gestione Android SDK](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

È necessario installare Android SDK Tools revisione 24.3.4 o versione successiva.
Per ulteriori informazioni sull'utilizzo di gestione Android SDK per installare Android 6,0 SDK, vedere [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Novell. Android

Creare un nuovo progetto Novell. Android. Se non si ha familiarità con lo sviluppo per Android con Novell, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti Android. 

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione per la destinazione di Android 6,0 MarshMallow. Per fare riferimento al progetto per marshmallow, è necessario configurare il progetto per il **livello API 23 (supporto per Novell. Android v 6.0)**. Per altre informazioni sulla configurazione dei livelli a livello di API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o un dispositivo

Se si usa un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo usando le impostazioni seguenti:

- Dispositivo: Nexus 5, 6 o 9.
- Destinazione: Android 6,0-livello API 23
- ABI: x86

Ad esempio, questo dispositivo virtuale è configurato per emulare un Nexus 5:

[![configurazione di un AVD usando il dispositivo Nexus 5, la destinazione Android 6,0 e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se si usa un dispositivo fisico, ad esempio Nexus 5, 6 o 9, è possibile installare un'immagine di anteprima di Android marshmallow. Per ulteriori informazioni sull'aggiornamento del dispositivo ad Android marshmallow, vedere [hardware System images](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Nuove funzionalità

Molte delle modifiche introdotte in Android marshmallow si concentrano sul miglioramento dell'esperienza utente Android, sull'aumento delle prestazioni e sulla correzione dei bug. Tuttavia, in marshmallow sono state introdotte anche alcune modifiche apportate alle nozioni di base della piattaforma Android. Le sezioni seguenti evidenziano questi miglioramenti e forniscono collegamenti che consentono di iniziare a usare le nuove funzionalità di Android marshmallow nell'app. 

### <a name="runtime-permissions"></a>Autorizzazioni di runtime

Il sistema di autorizzazioni Android è stato significativamente ottimizzato e semplificato da Android Lollipop. In Android marshmallow gli utenti concedono le autorizzazioni caso per caso in fase di esecuzione anziché in fase di installazione. Per supportare questa funzionalità in Android marshmallow e versioni successive, è necessario progettare l'applicazione in modo da richiedere all'utente le autorizzazioni in fase di esecuzione (nel contesto in cui sono necessarie le autorizzazioni). Questa modifica rende più semplice per gli utenti iniziare a usare immediatamente l'app perché semplifica il processo di installazione e aggiornamento dell'app. 

Vedere [richiesta di autorizzazioni di runtime in Android marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) per altri dettagli, inclusi esempi di codice, sull'implementazione delle autorizzazioni di runtime nelle app Novell. Android.
Novell fornisce anche un'app di esempio che illustra il funzionamento delle autorizzazioni di runtime in Android marshmallow (e versioni successive): [RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

Questa app di esempio illustra quanto segue:

- Come controllare e richiedere le autorizzazioni in fase di esecuzione.
- Come dichiarare le autorizzazioni per i dispositivi Android M.

Per usare questa app di esempio:

1. Toccare la **fotocamera** o i pulsanti **contatti** per visualizzare una finestra di dialogo di richiesta delle autorizzazioni.
2. Concedere l'autorizzazione per visualizzare frammenti di fotocamera o di contatti.

Per ulteriori informazioni sulle nuove funzionalità per le autorizzazioni di runtime in Android marshmallow, vedere [utilizzo delle autorizzazioni di sistema](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Miglioramenti dell'autenticazione

Android marshmallow include due miglioramenti dell'autenticazione che consentono di eliminare la necessità di password:

- **L'autenticazione con impronta digitale** &ndash; usa un'analisi delle impronte digitali per autenticare gli utenti.

- **Confermare le credenziali** &ndash; autentica gli utenti in base al tempo di sblocco del dispositivo.

I collegamenti e le app di esempio descritte di seguito possono essere utili per acquisire familiarità con le nuove funzionalità.

#### <a name="fingerprint-authentication"></a>Autenticazione con impronta digitale

Nei dispositivi che supportano l'hardware di analisi delle impronte digitali, è possibile usare la nuova classe `FingerPrintManager` per autenticare un utente.
Per altre informazioni sulla funzionalità di autenticazione con impronta digitale in Android marshmallow, vedere [l'autenticazione con impronta digitale](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Novell fornisce un'app di esempio che illustra come usare le impronte digitali registrate per autenticare un utente nell'app: [FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Per usare questa app di esempio:

1. Toccare il pulsante **Acquista** per aprire una finestra di dialogo di autenticazione con impronta digitale.
2. Eseguire l'analisi nell'impronta digitale registrata per l'autenticazione.

Si noti che questa app di esempio richiede un dispositivo con un lettore di impronte digitali.
Questa app non archivia l'impronta digitale (o la password).

#### <a name="voice-interactions"></a>Interazioni vocali

La nuova funzionalità di interazione vocale introdotta in Android marshmallow consente agli utenti dell'app di usare la propria voce per confermare le azioni e selezionare da un elenco di opzioni. Per altre informazioni sulle interazioni vocali, vedere [Panoramica dell'API di interazione vocale](https://developers.google.com/voice-actions/interaction/). 

Vedere [aggiungere una conversazione all'app Android con interazioni vocali](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) per altri dettagli, inclusi esempi di codice, sull'implementazione di interazioni vocali nelle app Novell. Android.
È disponibile un'app di esempio che illustra come usare l'API di interazione vocale in un'app Novell. Android: [interazioni vocali](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Conferma credenziali

Con la nuova funzionalità *Confirm Credentials* di Android marshmallow è possibile liberare gli utenti dal dover ricordare e immettere le password specifiche dell'app eseguendone l'autenticazione in base al tempo di sblocco del dispositivo.
A tale scopo, utilizzare il nuovo metodo `SetUserAuthenticationValidityDurationSeconds` del `KeyGenerator`. Usare il metodo `CreateConfirmDeviceCredentialIntent` di `KeyGuardManager`per autenticare di nuovo l'utente dall'interno dell'app. Per ulteriori informazioni su questa nuova funzionalità di Android marshmallow, vedere [Confirm Credential](https://developer.android.com/preview/api-overview.html#confirm-credential).

Novell fornisce un'app di esempio che illustra come usare le credenziali del dispositivo (ad esempio PIN, pattern o password) nell'app: [ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Per usare questa app di esempio:

1. Configurare una schermata di blocco sicura nel dispositivo (**secure > Security > ScreenLock**).
2. Toccare il pulsante **Acquista** e confermare le credenziali della schermata di blocco sicura.

### <a name="chrome-custom-tabs"></a>Schede personalizzate Chrome

Gli sviluppatori di app hanno la possibilità di scegliere quando un utente tocca un URL: l'app può avviare un browser o usare un browser in-App basato su un `WebView`. Entrambe le opzioni presentano problemi &ndash; l'avvio del browser è un cambio di contesto intenso che non è personalizzabile, mentre `WebView`non condividono lo stato con il browser. Inoltre, l'utilizzo di `WebView`s può aggiungere ulteriore overhead di manutenzione. 

Le *schede personalizzate Chrome* consentono di visualizzare in modo semplice ed elegante i siti Web con la potenza di Chrome senza che gli utenti lascino l'app. Questa funzionalità consente all'app di avere un maggiore controllo sull'esperienza Web dell'utente. consente di eseguire transizioni tra contenuto nativo e Web in modo più trasparente senza dover ricorrere a una `WebView`. L'app può anche influire sul modo in cui Chrome si presenta, personalizzando gli elementi seguenti: 

- Colore della barra degli strumenti

- Animazioni di immissione e uscita

- Azioni personalizzate nella barra degli strumenti e nel menu di overflow di Chrome

- Pre-avvio e prelettura del contenuto di Chrome (per un caricamento più rapido)

Per sfruttare i vantaggi di questa funzionalità nell'app Novell. Android, scaricare e installare la [libreria di schede personalizzate del supporto Android](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Per altre informazioni su questa funzionalità, vedere [schede personalizzate Chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Libreria di supporto della progettazione materiale

Android Lollipop ha introdotto la [progettazione del materiale](https://www.google.com/design/spec/material-design/introduction.html) come nuovo linguaggio di progettazione per aggiornare l'esperienza Android. per informazioni sull'uso della progettazione materiale nelle app Novell. Android, vedere [tema](~/android/user-interface/material-theme.md) del materiale. Con Android marshmallow, Google ha introdotto la libreria di supporto per la *progettazione Android* per semplificare l'adozione dell'aspetto della progettazione del materiale da parte degli sviluppatori di app. Questa libreria include i componenti seguenti:

- **CoordinatorLayout** &ndash; il nuovo widget `CoordinatorLayout` è simile a ma più potente di un `FrameLayout`. È possibile usare `CoordinatorLayout` come contenitore per le visualizzazioni figlio o come layout di primo livello e fornisce un attributo `layout_anchor` che può essere usato per ancorare le visualizzazioni rispetto ad altre viste.

- La **compressione delle barre degli strumenti** &ndash; nuova `CollapsingToolbarLayout` è una barra dell'app che è un wrapper per `Toolbar`. Si noti che la *barra dell'app* è quella che in precedenza era denominata *barra delle azioni*.

- **Pulsante di azione mobile** &ndash; un pulsante rotondo che indica l'azione principale sull'interfaccia dell'app.

- **Etichette mobili per la modifica di testo** &ndash; usa un nuovo widget `TextInputLayout` (che esegue il wrapping `EditText`) per visualizzare un'etichetta mobile quando un suggerimento è nascosto quando un utente immette testo.

- **Visualizzazione di navigazione** &ndash; il nuovo widget `NavigationView` consente di usare il pannello di navigazione in modo da semplificare la navigazione degli utenti.

- **Snackbar** &ndash; il nuovo widget `SnackBar` è un meccanismo di feedback leggero (simile a un avviso popup) che visualizza un breve messaggio nella parte inferiore dello schermo, che viene visualizzato sopra tutti gli altri elementi sullo schermo.

- **Schede materiali** &ndash; il nuovo widget `TabLayout` fornisce un layout orizzontale per la visualizzazione delle schede come modo per implementare la navigazione di primo livello nell'app.

Per sfruttare i vantaggi della [libreria di supporto della progettazione](https://developer.android.com/tools/support-library/features.html#design) nell'app Novell. Android, scaricare e installare il pacchetto NuGet di [progettazione della libreria di supporto Novell Novell](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) .

Per altri dettagli (inclusi esempi di codice) sull'uso della libreria di supporto della progettazione materiale nelle app Novell. Android, vedere la pagina relativa alla [progettazione di materiali accattivanti con la libreria di progettazione del supporto Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) .
Novell fornisce un'app di esempio che illustra la nuova libreria di progettazione Android in Novell. Android &ndash; [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare).
In questo esempio vengono illustrate le seguenti funzionalità della libreria di progettazione:

- Barra degli strumenti con compressione
- Pulsante azione mobile
- Visualizzazione ancoraggio
- NavigationView
- Snackbar

Per ulteriori informazioni sulla libreria di progettazione, vedere la pagina relativa alla [libreria di supporto della progettazione Android](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) nel Blog per sviluppatori Android.

### <a name="additional-library-updates"></a>Aggiornamenti aggiuntivi della libreria

Oltre ad Android marshmallow, Google ha annunciato aggiornamenti correlati a diverse librerie Android di base. Novell fornisce il supporto di Novell. Android per questi aggiornamenti tramite diversi pacchetti NuGet di versione di anteprima: 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; la versione più recente di Google Play Services include la nuova funzionalità *invita l'app* , che consente agli utenti di condividere l'app con gli amici. Per altre informazioni su questa funzionalità, vedere [espandere la portata dell'app con gli inviti dell'app di Google](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- Le [librerie di supporto android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; questi pacchetti NuGet offrono funzionalità disponibili solo per le API della libreria, fornendo versioni compatibili con le versioni precedenti delle API del Framework Android. 

- [Android Wearable Library](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; questo NuGet include Google Play Services associazioni. La versione più recente della libreria indossabile offre nuove funzionalità, inclusa la navigazione più semplice per le app personalizzate, alla piattaforma Android Wear. 

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato Android marshmallow e spiega come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Novell. Android in marshmallow. Viene inoltre fornita una panoramica delle nuove funzionalità più interessanti di Android marshmallow per lo sviluppo di Novell. Android.

## <a name="related-links"></a>Collegamenti correlati

- [Android 6,0 marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Ottenere il Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Panoramica delle funzionalità](https://developer.android.com/preview/api-overview.html)
- [Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
