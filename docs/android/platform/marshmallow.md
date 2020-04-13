---
title: Caratteristiche di Marshmallow
description: Questo articolo ti aiuta a iniziare a usare Xamarin.Android per sviluppare app per Android 6.0 Marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: fb1ba92be9527d490b3d34bd4c0e454b0a750837
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019982"
---
# <a name="marshmallow-features"></a>Caratteristiche di Marshmallow

_Questo articolo ti aiuta a iniziare a usare Xamarin.Android per sviluppare app per Android 6.0 Marshmallow._

Questo articolo fornisce una struttura delle nuove funzionalità di Android 6.0 Marshmallow, spiega come preparare lo sviluppo di Xamarin.Android per Android Marshmallow e fornisce collegamenti ad applicazioni di esempio che illustrano come utilizzare le nuove funzionalità di Android Marshmallow nelle app Xamarin.Android. 

## <a name="overview"></a>Panoramica

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), è la prossima grande versione di Android dopo Android Lollipop.
Xamarin.Android supporta Android Marshmallow e include:

- **API 23/Android 6.0 Bindings** &ndash; Android 6.0 aggiunge molte nuove API per le nuove funzionalità descritte di seguito; queste API sono disponibili per le app Xamarin.Android quando scegli come target l'API Level 23. Per ulteriori informazioni sulle API di Android 6.0, vedere [API di Android 6.0](https://developer.android.com/preview/api-overview.html). 

[![Immagini eroe di tablet e telefoni che eseguono Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Anche se il rilascio di Marshmallow si concentra principalmente su "polish e quality", fornisce anche molte nuove funzionalità di interesse per gli sviluppatori Xamarin.Android. Queste funzionalità includono: 

- **Autorizzazioni** &ndash; di runtime Questo miglioramento consente agli utenti di approvare le autorizzazioni di sicurezza caso per caso in fase di esecuzione. 

- &ndash; **Miglioramenti dell'autenticazione** A partire da Android Marshmallow, le app possono ora usare i sensori di impronte digitali per autenticare gli utenti e una nuova funzionalità di *conferma delle credenziali* riduce al minimo la necessità di immettere password. 

- **Collegamento di** &ndash; app Questa funzionalità consente di eliminare la necessità di visualizzare il **pop-up App Chooser** associando automaticamente le app ai domini Web. 

- **Condivisione** &ndash; diretta È possibile definire obiettivi di *condivisione diretta* che rendono la condivisione rapida e intuitiva per gli utenti; questa funzione consente agli utenti di condividere contenuti con altre app. 

- **Interazioni vocali** &ndash; Questa nuova API ti consente di creare funzionalità vocali conversazionali nella tua app. 

- **Modalità** &ndash; di visualizzazione 4K In Android Marshmallow, l'app può richiedere una risoluzione dello schermo 4K su hardware che la supporta. 

- **Nuove funzionalità** &ndash; audio a partire da Marshmallow, Android ora supporta il protocollo MIDI. Fornisce inoltre nuove classi per creare oggetti di acquisizione e riproduzione audio digitali e offre nuovi hook API per l'associazione di dispositivi audio e di input. 

- **Nuove funzionalità** &ndash; video Marshmallow fornisce una nuova classe che aiuta le app a eseguire il rendering dei flussi audio e video in sincronia; questa classe fornisce anche il supporto per la velocità di riproduzione dinamica. 

- **Android for Work** &ndash; Marshmallow include controlli avanzati per i dispositivi aziendali per utente singolo. Supporta l'installazione e la disinstallazione inattive delle app da parte del proprietario del dispositivo, l'accettazione automatica degli aggiornamenti di sistema, una migliore gestione dei certificati, il monitoraggio dell'utilizzo dei dati, la gestione delle autorizzazioni e le notifiche sullo stato di lavoro. 

- **Libreria** &ndash; supporto per la progettazione dei materiali La nuova libreria di supporto *tecnico* fornisce componenti e modelli di progettazione che semplificano la creazione dell'aspetto di Material Design nell'app. 

Inoltre, molti aggiornamenti di base della libreria Android sono stati rilasciati con Android M, e questi aggiornamenti forniscono nuove funzionalità sia per Android M che per le versioni precedenti di Android.

Inoltre, molti aggiornamenti di base della libreria Android sono stati rilasciati con Android Marshmallow, e questi aggiornamenti forniscono nuove funzionalità sia per Android Marshmallow che per le versioni precedenti di Android. Questo articolo spiega come iniziare a creare app con Android Marshmallow e fornisce una panoramica delle nuove funzionalità in Android 6.0. 

## <a name="requirements"></a>Requisiti

Per utilizzare le nuove funzionalità di Android Marshmallow nelle app basate su Xamarin è necessario quanto segue: 

- **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 o versione successiva deve essere installato e configurato con Visual Studio o Xamarin Studio.

- **Visual Studio per Mac** o **Visual Studio** &ndash; Se si usa Visual Studio per Mac, è necessaria la versione 5.9.7.22 o successiva. Se si utilizza Visual Studio, è necessaria la versione 3.11.1537 o successiva degli strumenti Xamarin per Visual Studio. 

- **Android SDK** &ndash; Android SDK 6.0 (API 23) o versione successiva deve essere installato tramite Android SDK Manager.

- **Java Developer Kit** &ndash; Xamarin.Android richiede [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sta sviluppando per il livello API 24 o superiore (JDK 1.8 supporta anche i livelli API precedenti a 24, incluso Marshmallow). La versione a 64 bit di JDK 1.8 è necessaria se si utilizzano controlli personalizzati o Forms Previewer.

È possibile continuare a utilizzare [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si sviluppa specificamente per il livello API 23 o versioni precedenti. 

## <a name="getting-started"></a>Introduzione

Per iniziare a utilizzare Android Marshmallow con Xamarin.Android, è necessario scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare un progetto Android Marshmallow: 

1. Installare gli ultimi aggiornamenti Xamarin dal canale **Stabile.** 

2. Installare i pacchetti e gli strumenti SDK di Marshmallow per Android 6.0.

3. Creare un nuovo progetto Xamarin.Android destinato ad Android 6.0 Marshmallow (livello API 23). 

4. Configurare un emulatore o un dispositivo per Android Marshmallow.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:Each of these steps is explained in the following sections:

### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Xamarin

Per aggiornare Xamarin in modo che includa il supporto per Android 6.0 Marshmallow, modificare il canale di aggiornamento in **Stabile** e installare tutti gli aggiornamenti. Per ulteriori informazioni sull'installazione degli aggiornamenti dal canale degli aggiornamenti, vedere [Modificare il canale degli aggiornamenti](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Installare Android 6.0 SDK

Per creare un progetto Xamarin.Android per Android Marshmallow, devi prima usare Android SDK Manager per installare Android 6.0 SDK:

- Avviare Android SDK Manager (in Visual Studio per Mac, usare **Tools > SDK Manager**in Visual Studio, usare Tools > Android > Android SDK **Manager)** e installare gli strumenti più recenti di Android SDK:

    [![Selezione degli strumenti di Android SDK in Android SDK Manager](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Inoltre, installare i pacchetti **SDK Android 6.0** più recenti:

    [![Selezione dei pacchetti SDK di Android 6.0 in Android SDK Manager](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

È necessario installare la revisione di Android SDK Tools 24.3.4 o versione successiva.
Per ulteriori informazioni sull'utilizzo di Android SDK Manager per installare Android 6.0 SDK, vedere [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Xamarin.Android

Creare un nuovo progetto Xamarin.Android.Create a new Xamarin.Android project. Se non si ha familiarità con lo sviluppo Android con Xamarin, vedere Hello, Android per informazioni sulla creazione di progetti Android.If you are new to Android development with Xamarin, see [Hello, Android](~/android/get-started/hello-android/index.md) to learn about creating Android projects. 

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione per Android 6.0 MarshMallow di destinazione. Per scegliere come destinazione il progetto per Marshmallow, è necessario configurare il progetto per il **livello API 23 (supporto Xamarin.Android v6.0)**. Per altre informazioni sulla configurazione dei livelli di API Android, vedere [Informazioni sui livelli](~/android/app-fundamentals/android-api-levels.md)di API Android .

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o un dispositivo

Se si utilizza un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo utilizzando le seguenti impostazioni:

- Dispositivo: Nexus 5, 6 o 9.
- Destinazione: Android 6.0 - Livello API 23Target: Android 6.0 - API Level 23
- ABI: x86

Ad esempio, questo dispositivo virtuale è configurato per emulare un Nexus 5:For example, this virtual device is configured to emulate a Nexus 5:

[![Configurazione di un AVD utilizzando il dispositivo Nexus 5, Android 6.0 Target e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se si utilizza un dispositivo fisico, ad esempio un Nexus 5, 6 o 9, è possibile installare un'immagine di anteprima di Android Marshmallow. Per ulteriori informazioni sull'aggiornamento del dispositivo ad Android Marshmallow, vedere [Immagini del sistema hardware](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Nuove funzionalità

Molte delle modifiche introdotte in Android Marshmallow sono incentrate sul miglioramento dell'esperienza utente Android, sull'aumento delle prestazioni e sulla correzione dei bug. Tuttavia, Marshmallow ha anche introdotto alcune modifiche estese ai fondamenti della piattaforma Android. Le sezioni seguenti evidenziano questi miglioramenti e forniscono collegamenti che consentono di iniziare a usare le nuove funzionalità di Android Marshmallow nell'app. 

### <a name="runtime-permissions"></a>Autorizzazioni di runtime

Il sistema di autorizzazioni Android è stato notevolmente ottimizzato e semplificato da Android Lollipop. In Android Marshmallow, gli utenti concedono le autorizzazioni caso per caso in fase di esecuzione anziché in fase di installazione. Per supportare questa funzionalità in Android Marshmallow e versioni successive, devi progettare l'app in modo da richiedere all'utente le autorizzazioni in fase di esecuzione (nel contesto in cui sono necessarie le autorizzazioni). Questa modifica semplifica l'utilizzo immediato dell'app da parte degli utenti perché semplifica il processo di installazione e aggiornamento dell'app. 

Per ulteriori dettagli sull'implementazione delle autorizzazioni di runtime nelle app Xamarin.Android, vedere Richiesta di autorizzazioni di [runtime in Android Marshmallow.See Requesting Runtime Permissions in Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) for more details (including code examples) about implementing Runtime Permissions in Xamarin.Android apps.
Xamarin fornisce anche un'app di esempio che illustra il funzionamento delle autorizzazioni di runtime in Android Marshmallow (e versioni successive): [RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

Questa app di esempio illustra quanto segue:This sample app demonstrates the following:

- Come controllare e richiedere le autorizzazioni in fase di esecuzione.
- Come dichiarare le autorizzazioni per i dispositivi Android M.

Per usare questa app di esempio:To use this sample app:

1. Toccare i pulsanti **Fotocamera** o **Contatti** per visualizzare una finestra di dialogo di richiesta di autorizzazioni.
2. Concedere l'autorizzazione per visualizzare i frammenti Fotocamera o Contatti.

Per ulteriori informazioni sulle nuove funzionalità delle autorizzazioni di runtime in Android Marshmallow, vedere [Utilizzo delle autorizzazioni](https://developer.android.com/preview/features/runtime-permissions.html)di sistema .

### <a name="authentication-enhancements"></a>Miglioramenti dell'autenticazione

Android Marshmallow include due miglioramenti di autenticazione che consentono di eliminare la necessità di password:

- **Autenticazione** &ndash; delle impronte digitali Utilizza una scansione delle impronte digitali per autenticare gli utenti.

- **Conferma credenziali** &ndash; Autentica gli utenti in base alla durata di sblocco del dispositivo.

I collegamenti e le app di esempio descritti di seguito consentono di acquisire familiarità con queste nuove funzionalità.

#### <a name="fingerprint-authentication"></a>Autenticazione con impronta digitale

Nei dispositivi che supportano l'hardware `FingerPrintManager` di scansione delle impronte digitali, è possibile utilizzare la nuova classe per autenticare un utente.
Per ulteriori informazioni sulla funzionalità di autenticazione delle impronte digitali in Android Marshmallow, vedere [Autenticazione delle impronte digitali](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin fornisce un'app di esempio che illustra come usare le impronte digitali registrate per autenticare un utente nell'app: [FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Per usare questa app di esempio:To use this sample app:

1. Toccare il pulsante **Acquista** per aprire una finestra di dialogo di autenticazione delle impronte digitali.
2. Eseguire la scansione dell'impronta digitale registrata per eseguire l'autenticazione.

Tieni presente che questa app di esempio richiede un dispositivo con un lettore di impronte digitali.
Questa applicazione non memorizza la tua impronta digitale (o la tua password).

#### <a name="voice-interactions"></a>Interazioni vocali

La nuova funzionalità Interazioni vocali introdotta in Android Marshmallow consente agli utenti dell'app di usare la voce per confermare le azioni e selezionare da un elenco di opzioni. Per ulteriori informazioni sulle interazioni vocali, vedere [Panoramica dell'API di interazione vocale](https://developers.google.com/voice-actions/interaction/). 

Per altri dettagli (inclusi esempi di codice) sull'implementazione delle interazioni vocali nelle app Xamarin.Android, vedi [Aggiungere una conversazione all'app Android con interazioni vocali.](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)
È disponibile un'app di esempio che illustra come usare l'API di interazione vocale in un'app Xamarin.Android: [Interazioni vocali.](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)

#### <a name="confirm-credential"></a>Conferma credenziali

Utilizzando la nuova funzionalità di *conferma delle credenziali* di Android Marshmallow, è possibile liberare gli utenti di dover ricordare e inserire le password specifiche dell'app autenticandoli in base a quanto tempo il loro dispositivo è stato sbloccato.
A tale scopo, utilizzare `SetUserAuthenticationValidityDurationSeconds` il `KeyGenerator`nuovo metodo del file . Usa `KeyGuardManager`il `CreateConfirmDeviceCredentialIntent` metodo 's per autenticare nuovamente l'utente dall'interno dell'app. Per ulteriori informazioni su questa nuova funzionalità in Android Marshmallow, vedere [Confermare le credenziali](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin fornisce un'app di esempio che illustra come usare le credenziali del dispositivo (ad esempio PIN, pattern o password) nell'app: [ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Per usare questa app di esempio:To use this sample app:

1. Configurare una schermata di blocco sicura sul dispositivo **(Secure > Security > Screenlock**).
2. Toccare il pulsante **Acquista** e confermare le credenziali della schermata di blocco sicura.

### <a name="chrome-custom-tabs"></a>Schede personalizzate di Chrome

Gli sviluppatori di app devono scegliere quando un utente tocca un URL: l'app può `WebView`avviare un browser o utilizzare un browser in-app basato su un file . Entrambe le &ndash; opzioni presentano sfide l'avvio del browser `WebView`è un notevole interruttore di contesto che non è personalizzabile, mentre s non condividere lo stato con il browser. Inoltre, l'uso di s può aggiungere ulteriore sovraccarico di `WebView`manutenzione. 

*Chrome Custom Tabs* ti consente di visualizzare facilmente ed elegantemente i siti web con la potenza di Chrome senza che i tuoi utenti abbandonino la tua app. Questa funzionalità offre all'app un maggiore controllo sull'esperienza Web dell'utente. rende più fluide le transizioni tra i contenuti `WebView`nativi e web senza dover ricorrere a un file . La tua app può anche influire sull'aspetto e il ricorrenze di Chrome personalizzando quanto segue: 

- Colore della barra degli strumenti

- Entrare e uscire dalle animazioni

- Azioni personalizzate nella barra degli strumenti di Chrome e nel menu di overflow

- Pre-avvio di Chrome e prelettura dei contenuti (per un caricamento più rapido)

Per sfruttare questa funzionalità nell'app Xamarin.Android, scarica e installa la libreria di schede personalizzate di [supporto Android.](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)
Per ulteriori informazioni su questa funzione, consultate [Schede personalizzate di Chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Libreria di supporto per la progettazione dei materiali

Android Lollipop ha introdotto [Material Design](https://www.google.com/design/spec/material-design/introduction.html) come un nuovo linguaggio di progettazione per aggiornare l'esperienza Android (vedere [Tema materiale](~/android/user-interface/material-theme.md) per informazioni sull'utilizzo di progettazione dei materiali nelle applicazioni Xamarin.Android). Con Android Marshmallow, Google ha introdotto la libreria di supporto per il *design Android* per rendere più facile per gli sviluppatori di app adottare un aspetto di design dei materiali. Questa libreria include i seguenti componenti:

- **CoordinatorLayout** &ndash; Il `CoordinatorLayout` nuovo widget è simile `FrameLayout`ma più potente di un file . È possibile `CoordinatorLayout` utilizzare come contenitore per le viste figlio o `layout_anchor` come layout di primo livello e fornisce un attributo che può essere utilizzato per ancorare le viste rispetto ad altre viste.

- **Compressione delle barre degli strumenti** &ndash; La nuova `CollapsingToolbarLayout` è una `Toolbar`barra dell'app comprimita che è un wrapper per . (Si noti che la *barra dell'app* è ciò che in precedenza era indicato come la barra delle *azioni*.)

- **Pulsante** &ndash; azione mobile Pulsante rotondo che indica l'azione principale nell'interfaccia dell'app.

- **Etichette mobili per la modifica del testo** &ndash; Utilizza un nuovo `TextInputLayout` widget (che va a capo `EditText`) per mostrare un'etichetta mobile quando un suggerimento viene nascosto quando un utente immette testo.

- **Visualizzazione** &ndash; di `NavigationView` navigazione Il nuovo widget consente di utilizzare il cassetto di navigazione in un modo più semplice per gli utenti di spostarsi.

- **Snackbar** &ndash; Il `SnackBar` nuovo widget è un meccanismo di feedback leggero (simile a un avviso popup) che visualizza un breve messaggio nella parte inferiore dello schermo, visualizzato sopra tutti gli altri elementi sullo schermo.

- **Schede** &ndash; materiale `TabLayout` Il nuovo widget offre un layout orizzontale per la visualizzazione delle schede come modo per implementare la navigazione di primo livello nell'app.

Per sfruttare la [libreria](https://developer.android.com/tools/support-library/features.html#design) di supporto di progettazione nell'app Xamarin.Android, scaricare e installare il pacchetto Xamarin [Xamarin Support Library Design](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) NuGet.

Per ulteriori dettagli sull'utilizzo della libreria di supporto per la progettazione dei materiali nelle app Xamarin.Android, vedere Beautiful Material Design with the Android Support Design Library (libreria di progettazione del [supporto Android).](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)
Xamarin fornisce un'app di esempio che illustra la nuova &ndash; libreria di progettazione Android su Xamarin.Android [Cheesesquare.](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare)
In questo esempio vengono illustrate le seguenti funzionalità della libreria di progetti:

- Barra degli strumenti Compressione
- Pulsante di azione mobile
- Ancoraggio della vista
- NavigationView
- Snackbar

Per altre informazioni sulla libreria di progettazione, vedere Libreria di supporto [di progettazione Android](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) nel blog di Android Developer.For more information about the Design library, see Android Design Support Library in the Android Developer's blog.

### <a name="additional-library-updates"></a>Ulteriori aggiornamenti della libreria

Oltre ad Android Marshmallow, Google ha annunciato aggiornamenti correlati a diverse librerie Android di base. Xamarin fornisce supporto Xamarin.Android per questi aggiornamenti tramite diversi pacchetti NuGet versione di anteprima:Xamarin provides Xamarin.Android support for these updates through several preview-release NuGet packages: 

- [Servizi](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; Google Play L'ultima versione di Google Play Services include la nuova funzione *Inviti* app, che consente agli utenti di condividere la propria app con gli amici. Per ulteriori informazioni su questa funzione, consulta [Espandere la copertura della tua app con gli inviti](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)alle app di Google. 

- [Librerie](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; di supporto Android Queste nuGets offrono funzionalità che sono disponibili solo per le API della libreria, fornendo versioni con versioni precedenti delle API del framework Android. 

- [Android Wearable Library](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; questo NuGet include binding di Google Play Services. L'ultima versione della libreria indossabile porta nuove funzionalità (tra cui una navigazione più semplice per le applicazioni personalizzate) alla piattaforma Android Wear. 

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto Android Marshmallow e ha spiegato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Xamarin.Android su Marshmallow. Ha anche fornito una panoramica delle nuove e interessanti funzionalità di Android Marshmallow per lo sviluppo di Xamarin.Android.

## <a name="related-links"></a>Collegamenti correlati

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Scarica l'SDK di Android](https://developer.android.com/sdk/index.html#Other)
- [Panoramica delle funzionalità](https://developer.android.com/preview/api-overview.html)
- [Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (esempio)RuntimePermissions (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (esempio)ConfirmCredential (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (esempio)FingerprintDialog (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
