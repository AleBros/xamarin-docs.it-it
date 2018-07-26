---
title: Funzionalità marshmallow
description: Questo articolo illustra come iniziare a usare con xamarin. Android per sviluppare App per Android Marshmallow 6.0.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 4f0bcd25662d3def719a89ccf833e845eb1728f2
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242238"
---
# <a name="marshmallow-features"></a>Funzionalità marshmallow

_Questo articolo illustra come iniziare a usare con xamarin. Android per sviluppare App per Android Marshmallow 6.0._

Questo articolo riporta un riepilogo delle nuove funzionalità in Android Marshmallow 6.0, spiega come preparare xamarin. Android per lo sviluppo di Android Marshmallow e vengono forniti collegamenti ad applicazioni di esempio che illustrano come utilizzare di nuovo Android Marshmallow funzionalità nelle App xamarin. Android. 


## <a name="overview"></a>Panoramica

[Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html), è il successivo Android principali release dopo Android Lollipop.
Xamarin. Android supporta Android Marshmallow e include:

-   **Associazioni API 23/Android 6.0** &ndash; Android 6.0 aggiunge molte nuove API per le nuove funzionalità descritte di seguito, queste API sono disponibili per le app xamarin. Android quando si destina livello API 23. Per altre informazioni sulle API di Android 6.0, vedere [API di Android 6.0](http://developer.android.com/preview/api-overview.html). 

[![Immagini di logo alto dei Tablet e telefoni esecuzione Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Anche se il rilascio Marshmallow è incentrato principalmente su "per il polacco e qualità", fornisce inoltre numerose nuove funzionalità di interesse per gli sviluppatori di xamarin. Android. Queste funzionalità comprendono: 

-   **Autorizzazioni di runtime** &ndash; questa funzionalità avanzata consente agli utenti di approvare le autorizzazioni di sicurezza caso per caso fase di esecuzione. 

-   **Miglioramenti di autenticazione** &ndash; a partire da Android Marshmallow, le app possono ora usare sensori di impronte digitali per autenticare gli utenti e un nuovo *conferma delle credenziali* funzionalità riduce al minimo la necessità per l'immissione password. 

-   **Collegamento di app** &ndash; questa funzionalità consente di eliminare la necessità del **selezione App** popup associando automaticamente le app con domini web. 

-   **Condivisione di indirizzare** &ndash; è possibile definire *indirizzare le destinazioni di condivisione* che rendono la condivisione rapida e intuitiva per gli utenti, mentre questa funzionalità da parte degli utenti a condividere contenuto con altre app. 

-   **Le interazioni vocali** &ndash; questa nuova API consente di integrare le funzionalità vocali discorsive nella tua app. 

-   **Modalità di visualizzazione 4 K** &ndash; In Android Marshmallow, l'app può richiedere un 4K risoluzione dello schermo su hardware che la supporta. 

-   **Nuove funzionalità Audio** &ndash; partire Marshmallow, Android supporta ora il protocollo MIDI. Fornisce inoltre nuove classi per creare oggetti di riproduzione e acquisizione audio digitale, e offre nuove API hook per associare i dispositivi audio e inpui. 

-   **Nuove funzionalità Video** &ndash; Marshmallow fornisce una nuova classe che le app possono eseguire il rendering di flussi audio e video sincronizzati, questa classe fornisce inoltre supporto per la velocità di riproduzione dinamica. 

-   **Android for Work** &ndash; Marshmallow include controlli avanzati per i dispositivi di proprietà aziendale, l'utente singolo. Supporta l'installazione invisibile all'utente e la disinstallazione dell'App dal proprietario del dispositivo, accettazione automatica di aggiornamenti del sistema, gestione migliorata dei certificati, il rilevamento dell'utilizzo di dati, la gestione delle autorizzazioni e le notifiche dello stato del lavoro. 

-   **Material Design Library di supporto** &ndash; nuova *libreria di supporto di progettazione* fornisce componenti di progettazione e modelli che rende più semplice per poter integrare Material Design aspetto nella tua app. 

Inoltre, sono stati rilasciati numerosi aggiornamenti di core della libreria Android con Android M e questi aggiornamenti offrono nuove funzionalità per Android M e versioni precedenti di Android.

Inoltre, sono stati rilasciati numerosi aggiornamenti di core della libreria Android con Android Marshmallow, e gli aggiornamenti offrono nuove funzionalità per Android Marshmallow e versioni precedenti di Android. Questo articolo illustra come iniziare a creare App con Android Marshmallow, e fornisce che una panoramica della nuova funzionalità di evidenziazione in Android 6.0. 

## <a name="requirements"></a>Requisiti

Per utilizzare le nuove funzionalità di Android Marshmallow nelle App basate su Xamarin è necessario quanto segue: 

-   **Xamarin. Android** &ndash; xamarin. Android 5.1.7.12 o versioni successive deve essere installato e configurato con Visual Studio o Xamarin Studio.

-   **Visual Studio per Mac** oppure **Visual Studio** &ndash; se si usa Visual Studio per Mac, versione 5.9.7.22 o versione successiva è richiesto. Se si usa Visual Studio, versione 3.11.1537 o versione successiva degli strumenti di Xamarin per Visual Studio è necessaria. 

-   **Android SDK** &ndash; Android 6.0 SDK (API 23) o versione successiva deve essere installato tramite Android SDK Manager.

-   **Java Developer Kit** &ndash; xamarin. Android richiede [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sviluppa per il livello API 24 o versione successiva (JDK 1.8 supporta anche i livelli API precedenti a 24, tra cui Marshmallow). Se si usa il Visualizzatore anteprima del form o controlli personalizzati, è necessaria la versione a 64 bit di JDK 1.8.

È possibile continuare a usare [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si è lo sviluppo in modo specifico per il livello API 23 o versioni precedenti. 


## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android Marshmallow con xamarin. Android, è necessario scaricare e installare gli strumenti più recenti e i pacchetti SDK prima di poter creare un progetto Android Marshmallow: 

1.  Installare gli ultimi aggiornamenti di Xamarin dal **stabile** canale. 

2.  Installare i pacchetti Android 6.0 Marshmallow SDK e strumenti.

3.  Creare un nuovo progetto xamarin. Android destinata a Android 6.0 Marshmallow (API livello 23). 

4.  Configurare un dispositivo o emulatore per Android Marshmallow.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:


### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Xamarin

Per aggiornare Xamarin in modo che includa il supporto per Android Marshmallow 6.0, modificare il canale di aggiornamento **stabile** e installare tutti gli aggiornamenti. Per altre informazioni sull'installazione degli aggiornamenti dal canale degli aggiornamenti, vedere [modificare il canale aggiornamenti](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 


### <a name="install-the-android-60-sdk"></a>Installare il SDK Android 6.0

Per creare un progetto xamarin. Android per Android Marshmallow, è necessario innanzitutto di Android SDK Manager per installare Android SDK 6.0:

-   Avviare Android SDK Manager (in Visual Studio per Mac, usare **strumenti > SDK Manager**; in Visual Studio, utilizzano **strumenti > Android > Android SDK Manager**) e installare il più recente di Android SDK Tools:

    [![Selezione di Android SDK tools di Android SDK Manager](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   Inoltre, installare la versione più recente **Android 6.0** pacchetti SDK:

    [![Selezione pacchetti Android 6.0 SDK in Android SDK Manager](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

È necessario installare la revisione di Android SDK Tools 24.3.4 o versione successiva.
Per altre informazioni sull'uso di Android SDK Manager per installare Android SDK 6.0, vedere [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin. Android

Creare un nuovo progetto xamarin. Android. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti di Android. 

Quando si crea un progetto Android, è necessario configurare le impostazioni di versione di destinazione Android MarshMallow 6.0. Per impostare come destinazione del progetto per Marshmallow, è necessario configurare il progetto per la **livello API 23 (xamarin. Android versione 6.0 supporto)**. Per altre informazioni sulla configurazione dei livelli a livello API Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).



### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si usa un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo usando le impostazioni seguenti:

-   Dispositivo: Nexus 5, 6 o 9.
-   Destinazione: Android 6.0 - livello 23 dell'API
-   ABI: x86

Ad esempio, il dispositivo virtuale è configurato per emulare un Nexus 5:

[![Configurazione di un dispositivo virtuale Android usando dispositivi Nexus 5, destinazione di Android 6.0 e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se si usa un dispositivo fisico, ad esempio un Nexus 5, 6 e 9, è possibile installare un'immagine di anteprima di Android Marshmallow. Per altre informazioni sull'aggiornamento del dispositivo per Android Marshmallow, vedere [immagini del sistema Hardware](http://developer.android.com/preview/download.html#images).



## <a name="new-features"></a>Nuove funzionalità

Molte delle modifiche introdotte in Android Marshmallow sono incentrate sul miglioramento dell'esperienza utente di Android, aumentando le prestazioni e correggendo i bug. Tuttavia, Marshmallow introdotte anche alcune modifiche estese ai concetti fondamentali della piattaforma Android. Le sezioni seguenti evidenziare questi miglioramenti e forniscono i collegamenti che consentono di iniziare subito a usare le nuove funzionalità di Android Marshmallow nell'app. 



### <a name="runtime-permissions"></a>Autorizzazioni di runtime

Ottimizzato e semplificato poiché Android Lollipop il sistema di autorizzazioni di Android in modo significativo. In Android Marshmallow, gli utenti concedere le autorizzazioni su una base dal caso in fase di esecuzione anziché in fase di installazione. Per supportare questa funzionalità in Android Marshmallow e versioni successive, si progetta la tua app per richiedere all'utente le autorizzazioni in fase di esecuzione (nel contesto di in cui sono necessarie le autorizzazioni). Questa modifica rende più semplice per gli utenti iniziare a usare l'app immediatamente perché semplifica il processo di installazione e l'aggiornamento all'app. 

Visualizzare [richiesta di autorizzazioni di Runtime in Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) per altri dettagli (inclusi gli esempi di codice) sull'implementazione di autorizzazioni di Runtime nelle App xamarin. Android.
Xamarin offre inoltre un'app di esempio che illustra il funzionano delle autorizzazioni di runtime in Android Marshmallow (e versioni successive): [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions).

Questa app di esempio viene illustrato quanto segue:

-   Come controllare e richiedere le autorizzazioni in fase di esecuzione.
-   Viene descritto come dichiarare le autorizzazioni per i dispositivi Android M.

Per usare questa app di esempio:

1.  Toccare il **fotocamera** oppure **contatti** pulsanti da visualizzare le autorizzazioni di un dialogo di richiesta.
2.  Concedere l'autorizzazione per visualizzare i frammenti della fotocamera o i contatti.

Per altre informazioni sulle nuove funzionalità di autorizzazioni di runtime in Android Marshmallow, vedere [utilizzo delle autorizzazioni di sistema](https://developer.android.com/preview/features/runtime-permissions.html).



### <a name="authentication-enhancements"></a>Miglioramenti di autenticazione

Android Marshmallow include due miglioramenti di autenticazione che consentono di eliminare la necessità di password:

-   **L'autenticazione di impronte digitali** &ndash; Usa un'analisi di impronte digitali per autenticare gli utenti.

-   **Conferma delle credenziali** &ndash; autentica gli utenti basati su quanto tempo il dispositivo è stato sbloccato.

I collegamenti e le app di esempio descritte di seguito consente di acquisire familiarità con queste nuove funzionalità.


#### <a name="fingerprint-authentication"></a>Autenticazione tramite impronta digitale

Nei dispositivi che supportano analisi hardware delle impronte digitali, è possibile usare il nuovo `FingerPrintManager` classe per autenticare un utente.
Per altre informazioni sulla funzionalità per l'autenticazione tramite impronta digitale in Android Marshmallow, vedere [autenticazione tramite impronta digitale](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin offre un'app di esempio che illustra come usare le impronte digitali registrate per autenticare un utente nell'app: [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog).

Per usare questa app di esempio:

1.  Toccare il **acquisto** pulsante per aprire una finestra di dialogo di autenticazione tramite impronta digitale.
2.  Analizzare nella propria impronta digitale registrata per l'autenticazione.

Si noti che questa app di esempio richiede un dispositivo con un lettore di impronte digitali.
Questa app non archivia l'impronta digitale (o la password).



#### <a name="voice-interactions"></a>Interazioni vocali

La nuova funzionalità di interazioni vocali introdotte in Android Marshmallow consente agli utenti dell'app di usare comandi vocali per verificare le azioni e selezionare un elenco di opzioni. Per altre informazioni sulle interazioni vocali, vedere [panoramica dell'API di interazioni vocali](https://developers.google.com/voice-actions/interaction/). 

Visualizzare [aggiungere una conversazione all'App Android con le interazioni vocali](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) per altri dettagli (inclusi gli esempi di codice) sull'implementazione di interazioni vocali nelle App xamarin. Android.
È disponibile un'app di esempio che illustra come usare l'API di interazioni vocali in un'app xamarin. Android: [interazioni vocali](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).



#### <a name="confirm-credential"></a>Conferma delle credenziali

Usando le nuove *confermare le credenziali* funzionalità di Android Marshmallow, è possibile liberare agli utenti di dover ricordare e immettere le password di app mediante l'autenticazione in base a quanto tempo il dispositivo è stato sbloccato.
A tale scopo, si utilizza la nuova `SetUserAuthenticationValidityDurationSeconds` metodo di `KeyGenerator`. Usare la `KeyGuardManager`del `CreateConfirmDeviceCredentialIntent` metodo autenticare nuovamente l'utente dall'interno dell'app. Per altre informazioni su questa nuova funzionalità di Android Marshmallow, vedere [confermare le credenziali](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin offre un'app di esempio che illustra come usare le credenziali del dispositivo (ad esempio, PIN, modello o password) nell'app: [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

Per usare questa app di esempio:

1.  Configurare una schermata di blocco protetta nel dispositivo (**sicura > sicurezza > Screenlock**).
2.  Toccare il **acquisto** pulsante e verificare le credenziali di schermata di blocco protetta.



### <a name="chrome-custom-tabs"></a>Schede personalizzate di Chrome

Gli sviluppatori di App devono affrontare una scelta quando un utente tocca un URL: l'app può avviare un browser oppure usare un browser in-app basate su un `WebView`. Entrambe le opzioni presentano sfide &ndash; avvia il browser è un cambio di contesto eccessivo che non è personalizzabile, mentre `WebView`non condividano lo stato con il browser. Inoltre, usare di `WebView`s è possibile aggiungere un sovraccarico di manutenzione aggiuntive. 

*Chrome schede personalizzate* consente di visualizzare in modo elegante e facilmente siti Web con la potenza di Chrome senza lasciare l'app agli utenti. Questa funzionalità offre l'app più controllo sull'esperienza web dell'utente; rende le transizioni tra native e web più semplice il contenuto senza dover ricorrere a un `WebView`. L'app può incidere sulle modalità Chrome struttura e l'aspetto personalizzando il seguente: 

-   Colore della barra degli strumenti

-   Attivare e disattivare le animazioni

-   Azioni personalizzate nel menu della barra degli strumenti e l'overflow di Chrome

-   Chrome pre-avvio e il contenuto pre-recupero (per il caricamento più veloce)

Per sfruttare i vantaggi di questa funzionalità nell'app xamarin. Android, scaricare e installare il [schede libreria personalizzata di Android supporto](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Per altre informazioni su questa funzionalità, vedere [schede personalizzate Chrome](https://developer.chrome.com/multidevice/android/customtabs).



### <a name="material-design-support-library"></a>Libreria di supporto di Material Design

Android Lollipop introdotte [Material Design](http://www.google.com/design/spec/material-design/introduction.html) come un nuovo linguaggio di progettazione per aggiornare l'esperienza di Android (vedere [tema Material](~/android/user-interface/material-theme.md) per informazioni sull'utilizzo di progettazione dei materiali nelle App xamarin. Android). Con Android Marshmallow, Google introduce il *libreria di supporto di progettazione Android* per renderne più semplice per l'app che gli sviluppatori adottino materiale di progettano l'aspetto. Questa libreria include i componenti seguenti:

-   **CoordinatorLayout** &ndash; nuova `CoordinatorLayout` widget è simile ma più potente di un `FrameLayout`. È possibile usare `CoordinatorLayout` come un contenitore per le visualizzazioni figlio o come un layout di primo livello che fornisce un `layout_anchor` attributo che può essere utilizzato per le visualizzazioni di ancoraggio rispetto alla altre viste.

-   **Le barre degli strumenti di compressione** &ndash; nuova `CollapsingToolbarLayout` è una barra app compressione che è un wrapper per `Toolbar`. (Si noti che il *barra dell'app* è il era precedentemente noto come il *sulla barra delle azioni*.)

-   **Pulsante di azione mobile** &ndash; un pulsante circolare che denota l'azione principale nell'interfaccia dell'app.

-   **Mobile etichette per la modifica del testo** &ndash; Usa un nuovo `TextInputLayout` widget (che esegue il wrapping `EditText`) per visualizzare un'etichetta a virgola mobile quando un hint viene nascosto quando un utente immette un testo.

-   **Vista di navigazione** &ndash; nuovo `NavigationView` widget illustra come usare il menu di spostamento in un modo più semplice agli utenti di spostarsi.

-   **Snackbar** &ndash; nuovo `SnackBar` widget è un meccanismo di feedback leggeri (simile a un avviso popup) che viene visualizzato un messaggio breve nella parte inferiore della schermata, visualizzati sopra tutti gli altri elementi sullo schermo.

-   **Schede materiale** &ndash; nuovo `TabLayout` widget offre un layout orizzontale per la visualizzazione di schede come modo per implementare l'esplorazione di livello principale nell'app.

Per sfruttare il [Design Library di supporto](http://developer.android.com/tools/support-library/features.html#design) nell'app xamarin. Android, scaricare e installare il Xamarin [progettazione di librerie di supporto di Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) pacchetto NuGet.

Visualizzare [accattivanti Material Design con la libreria di progettazione di supporto Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) per altri dettagli (inclusi gli esempi di codice) sull'uso della libreria di supporto di materiale di progettazione nelle App xamarin. Android.
Xamarin offre un'app di esempio che dimostra la nuova libreria di progettazione per Android in xamarin. Android &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare).
Questo esempio illustra le funzionalità della libreria di progettazione seguenti:


-   Compressione della barra degli strumenti
-   Pulsante di azione mobile
-   Visualizzazione ancoraggio
-   NavigationView
-   Snackbar

Per altre informazioni sulla libreria di progettazione, vedere [libreria di supporto di progettazione Android](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) nel blog per gli sviluppatori di Android.


### <a name="additional-library-updates"></a>Aggiornamenti di librerie aggiuntive

Oltre a Android Marshmallow, Google ha annunciato aggiornamenti correlati alle diverse librerie di Android core. Xamarin offre il supporto di xamarin. Android per gli aggiornamenti tramite diversi pacchetti NuGet versione di anteprima: 

-   [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; la versione più recente di Google Play Services include la nuova *App Invites* funzionalità che consente agli utenti di condividere le app con gli amici. Per altre informazioni su questa funzionalità, vedere [Reach espandere dell'applicazione con Google App Invites](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

-   [Librerie di supporto Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; questi pacchetti NuGet sono disponibili caratteristiche sono disponibili solo per API della libreria, fornendo versioni compatibili dell'API del framework Android. 

-   [Libreria Android indossabile](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; questo NuGet include associazioni di Google Play Services. La versione più recente della libreria indossabile offre nuove funzionalità (ad esempio una navigazione più semplice per le app personalizzate) per la piattaforma Android Wear. 


## <a name="summary"></a>Riepilogo

Questo articolo introdotto Android Marshmallow ed è stato spiegato come installare e configurare gli strumenti e i pacchetti per lo sviluppo di xamarin. Android più recente in Marshmallow. Anche fornita una panoramica delle nuove funzionalità di Android Marshmallow più interessanti per lo sviluppo di xamarin. Android.


## <a name="related-links"></a>Collegamenti correlati

- [Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html)
- [Ottenere Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Panoramica delle funzionalità](https://developer.android.com/preview/api-overview.html)
- [Note sulla versione](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions (esempio)](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential (esempio)](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog (esempio)](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
