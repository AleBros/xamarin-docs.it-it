---
title: "Funzionalità marshmallow"
description: In questo articolo consente di iniziare a usare l'utilizzo di xamarin per sviluppare App per Android Marshmallow 6.0.
ms.topic: article
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b28ca68701394a8b7b0b543a5ae646910e7c8361
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="marshmallow-features"></a>Funzionalità marshmallow

_In questo articolo consente di iniziare a usare l'utilizzo di xamarin per sviluppare App per Android Marshmallow 6.0._

In questo articolo illustra le nuove funzionalità di Android Marshmallow 6.0, spiega come preparare xamarin per lo sviluppo di Android Marshmallow e vengono forniti collegamenti ad applicazioni di esempio che illustrano come utilizzare di nuovo Android Marshmallow caratteristiche in App xamarin. 

<a name="overview" />

## <a name="overview"></a>Panoramica

[Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html), è il successivo Android principali release dopo il simbolo Android.
Xamarin supporta Android Marshmallow e include:

-   **API 23/Android 6.0 associazioni** &ndash; Android 6.0 aggiunge molte nuove API per le nuove funzionalità descritte di seguito; queste API sono disponibili per le app xamarin quando la destinazione di livello API 23. Per ulteriori informazioni sulle API di Android 6.0, vedere [Android 6.0 API](http://developer.android.com/preview/api-overview.html). 

[![Immagini hero di Tablet e telefoni in esecuzione Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png)

Anche se la versione di Marshmallow è incentrata principalmente sul "per il polacco e qualità", fornisce inoltre numerose nuove funzionalità di interesse per gli sviluppatori di xamarin. Queste funzionalità comprendono: 

-   **Le autorizzazioni di runtime** &ndash; questa funzionalità avanzata consente agli utenti di approvare le autorizzazioni di sicurezza caso per caso fase di esecuzione. 

-   **Miglioramenti di autenticazione** &ndash; a partire da Android Marshmallow, App ora possono usare i sensori di impronte digitali per autenticare gli utenti e un nuovo *conferma delle credenziali* funzionalità riduce al minimo la necessità per l'immissione password. 

-   **Collegamento app** &ndash; questa funzionalità consente di eliminare la necessità di **selezione App** popup associando automaticamente le app con i domini di web. 

-   **Condivisione diretta** &ndash; è possibile definire *diretta destinazioni condivisione* che rendono la condivisione rapido e intuitivo per gli utenti; questa funzionalità consente uers di condividere il contenuto con altre app. 

-   **Voice interazioni** &ndash; questa nuova API consente di compilare funzionalità di conversazione a vocale nell'app. 

-   **Modalità di visualizzazione 4 K** &ndash; In Android Marshmallow l'app può richiedere un 4K risoluzione dello schermo su hardware che la supporta. 

-   **Nuove funzionalità Audio** &ndash; a partire da Marshmallow, Android supporta ora il protocollo MIDI. Fornisce inoltre nuove classi per creare oggetti di riproduzione e l'acquisizione audio digitale, e offre nuove API hook per l'associazione di dispositivi audio e di input. 

-   **Nuove funzionalità Video** &ndash; Marshmallow fornisce una nuova classe che consente di App di eseguire il rendering di flussi audio e video sincronizzati, questa classe fornisce anche supporto per la velocità di riproduzione dinamica. 

-   **Android for Work** &ndash; Marshmallow include controlli avanzati per i dispositivi di proprietà dell'azienda, l'utente singolo. Supporta l'installazione invisibile all'utente e la disinstallazione di applicazioni dal proprietario del dispositivo, l'accettazione automatica di aggiornamenti del sistema, la gestione dei certificati migliorata, il rilevamento dell'utilizzo di dati, gestione delle autorizzazioni e notifiche sullo stato del lavoro. 

-   **Libreria di supporto di progettazione materiale** &ndash; il nuovo *libreria di supporto di progettazione* fornisce componenti di progettazione e modelli che rende più semplice per la creazione di aspetto progettazione materiale nell'app. 

Inoltre, molti componenti di base libreria Android aggiornamenti sono stati rilasciati con Android M, e questi aggiornamenti offrono nuove funzionalità per Android M e versioni precedenti di Android.

Inoltre, molti componenti di base libreria Android aggiornamenti sono stati rilasciati con Android Marshmallow e questi aggiornamenti offrono nuove funzionalità per Android Marshmallow e versioni precedenti di Android. In questo articolo viene illustrato come iniziare a creare App con Android Marshmallow e fornisce che una panoramica della nuova funzionalità viene evidenziato in Android 6.0. 


<a name="requirements" />

## <a name="requirements"></a>Requisiti

Di seguito è necessario utilizzare le nuove funzionalità di Android Marshmallow nelle App basate su Xamarin: 

-   **Xamarin** &ndash; xamarin 5.1.7.12 o versioni successive deve essere installato e configurato con Visual Studio o Xamarin Studio.

-   **Visual Studio per Mac** o **Visual Studio** &ndash; se si utilizza Visual Studio per Mac, versione 5.9.7.22 o versione successiva è richiesto. Se si utilizza Visual Studio, versione 3.11.1537 o versione successiva degli strumenti di Xamarin per Visual Studio è obbligatoria. 

-   **Android SDK** &ndash; Android SDK 6.0 (API 23) o versioni successive deve essere installato mediante Android SDK Manager.

-   **Java Developer Kit** &ndash; xamarin richiede [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se sviluppano applicazioni per il livello di API 24 o superiore (JDK 1.8 supporta inoltre API livelli precedenti a 24, tra cui Marshmallow). La versione a 64 bit di JDK 1.8 è obbligatoria se si utilizza il Visualizzatore di form o di controlli personalizzati.

È possibile continuare a utilizzare [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) lo sviluppo in modo specifico per il livello API 23 o precedenti. 

<a name="gettingstarted" />

## <a name="getting-started"></a>Introduzione

Per iniziare a utilizzare Android Marshmallow con xamarin, è necessario scaricare e installare il più recente degli strumenti e i pacchetti SDK prima di poter creare un progetto Android Marshmallow: 

1.  Installare gli ultimi aggiornamenti di Xamarin dal **stabile** canale. 

2.  Installare gli strumenti e i pacchetti Android 6.0 Marshmallow SDK.

3.  Creare un nuovo progetto xamarin destinata Android 6.0 Marshmallow (API livello 23). 

4.  Configurare un dispositivo o l'emulatore per Android Marshmallow.

Ognuno di questi passaggi verrà illustrato nelle sezioni seguenti:

<a name="updates" />

### <a name="install-xamarin-updates"></a>Installare gli aggiornamenti di Xamarin

Per aggiornare Xamarin in modo che includa il supporto per Android Marshmallow 6.0, modificare il canale di aggiornamento per **stabile** e installare gli aggiornamenti. Per ulteriori informazioni sull'installazione degli aggiornamenti dal canale aggiornamenti, vedere [modificare il canale aggiornamenti](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/). 

<a name="sdkpreview" />

### <a name="install-the-android-60-sdk"></a>Installare Android SDK 6.0

Per creare un progetto xamarin per Android Marshmallow, è necessario utilizzare innanzitutto Android SDK Manager per installare Android SDK 6.0:

-   Avviare Android SDK Manager (in Visual Studio per Mac, usare **strumenti > SDK Manager**; in Visual Studio, usare **strumenti > Android > Android SDK Manager**) e installare strumenti di Android SDK più recente:

    [![Selezionare gli strumenti di Android SDK in Android SDK Manager](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png)

-   Inoltre, installare la versione più recente **Android 6.0** pacchetti SDK:

    [![Selezionare i pacchetti Android 6.0 SDK in Android SDK Manager](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png)

È necessario installare revisione strumenti Android SDK 24.3.4 o versione successiva.
Per ulteriori informazioni sull'uso di Android SDK Manager per installare Android SDK 6.0, vedere [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).


<a name="xaproject" />

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin

Creare un nuovo progetto di xamarin. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per informazioni sulla creazione di progetti Android. 

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione di destinazione Android MarshMallow 6.0. Per impostare come destinazione del progetto per Marshmallow, è necessario configurare il progetto per **livello API 23 (versione 6.0 xamarin supporto)**. Per ulteriori informazioni sulla configurazione dei livelli di livello API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).


<a name="emudev" />

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si utilizza un emulatore, avviare Android AVD Manager e creare un nuovo dispositivo usando le impostazioni seguenti:

-   Dispositivo: Nodo 5, 6 o 9.
-   Destinazione: Android 6.0 - livello API 23
-   ABI: x86

Ad esempio, questo dispositivo virtuale è configurato per emulare un 5 Nexus:

[![Configurazione di un AVD utilizzando Nexus 5 dispositivi Android 6.0 destinazione e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png)

Se si utilizza un dispositivo fisico, ad esempio un nodo di 5, 6 e 9, è possibile installare un'immagine di anteprima di Android Marshmallow. Per ulteriori informazioni sull'aggiornamento del dispositivo per Android Marshmallow, vedere [le immagini del sistema Hardware](http://developer.android.com/preview/download.html#images).


<a name="newfeatures" />

## <a name="new-features"></a>Nuove funzionalità

Molte delle modifiche introdotte in Android Marshmallow sono incentrate su migliorare l'esperienza utente Android, aumentando le prestazioni e la correzione di bug. Tuttavia, Marshmallow anche introdotte alcune modifiche estese per le nozioni di base della piattaforma Android. Nelle sezioni seguenti evidenziare questi miglioramenti e forniscono i collegamenti che consentono di iniziare a utilizzare le nuove funzionalità di Android Marshmallow nell'app. 


<a name="permissions" />

### <a name="runtime-permissions"></a>Autorizzazioni di runtime

Ottimizzato e semplificato dopo il simbolo Android il sistema di autorizzazioni di Android in modo significativo. In Android Marshmallow, gli utenti concedere autorizzazioni in base a caso per caso in fase di esecuzione anziché in fase di installazione. Per supportare questa funzionalità in Android Marshmallow e versioni successive, progettare le app per richiedere all'utente le autorizzazioni in fase di esecuzione (nel contesto di in cui sono necessarie le autorizzazioni). Questa modifica rende più semplice per gli utenti iniziare a usare l'app immediatamente perché semplifica il processo di installazione e aggiornamento dell'app. 

Vedere [richiesta di autorizzazioni di Runtime in Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) per ulteriori informazioni (con esempi di codice) sull'implementazione di autorizzazioni di Runtime nelle App xamarin.
Xamarin offre inoltre un'app di esempio che illustra il funzionamento delle autorizzazioni di runtime in Android Marshmallow (e versioni successive): [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions).

Questa app di esempio viene illustrato quanto segue:

-   Come controllare e richiedere le autorizzazioni in fase di esecuzione.
-   Come dichiarare le autorizzazioni per i dispositivi Android M.

Per usare questa app di esempio:

1.  Toccare il **fotocamera** o **contatti** pulsanti da visualizzare le autorizzazioni di un dialogo di richiesta.
2.  Concedere l'autorizzazione per visualizzare i frammenti della fotocamera o contatti.

Per ulteriori informazioni sulle nuove funzionalità di runtime delle autorizzazioni in Android Marshmallow, vedere [utilizzo delle autorizzazioni di sistema](https://developer.android.com/preview/features/runtime-permissions.html).


<a name="authentication" />

### <a name="authentication-enhancements"></a>Miglioramenti dell'autenticazione

Android Marshmallow include due miglioramenti di autenticazione che consentono di eliminare la necessità di password:

-   **L'autenticazione di impronte digitali** &ndash; utilizza un'analisi delle impronte digitali per autenticare gli utenti.

-   **Conferma delle credenziali** &ndash; autentica gli utenti in base a quanto tempo il dispositivo è stato sbloccato.

I collegamenti e App di esempio descritta di seguito consente di acquisire familiarità con queste nuove funzionalità.


<a name="fingerprint" />

#### <a name="fingerprint-authentication"></a>Autenticazione impronta digitale

Nei dispositivi che supportano analisi hardware delle impronte digitali, è possibile utilizzare il nuovo `FingerPrintManager` per autenticare un utente.
Per ulteriori informazioni sulla funzionalità di autenticazione impronta digitale in Android Marshmallow, vedere [autenticazione impronta digitale](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin offre un'app di esempio che illustra come usare le impronte digitali registrate per autenticare un utente nell'app: [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog).

Per usare questa app di esempio:

1.  Toccare il **acquisto** pulsante per aprire una finestra di dialogo di autenticazione impronta digitale.
2.  Scansione l'impronta digitale registrato per l'autenticazione.

Si noti che questa app di esempio richiede un dispositivo con un lettore di impronte digitali.
Questa app non archivia l'impronta digitale (o la password).


<a name="voice" />

#### <a name="voice-interactions"></a>Interazioni vocale

La nuova funzionalità di interazioni vocale introdotta in Android Marshmallow consente agli utenti dell'app di usare la voce per confermare le azioni e selezionare da un elenco di opzioni. Per ulteriori informazioni sulle interazioni vocali, vedere [panoramica dell'API di interazione vocale](https://developers.google.com/voice-actions/interaction/). 

Vedere [aggiungere una conversazione all'App Android con le interazioni vocale](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) per ulteriori informazioni (con esempi di codice) sull'implementazione di interazioni vocale in App xamarin.
È disponibile un'app di esempio che illustra come usare l'API di interazione vocale in un'app xamarin: [vocale interazioni](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).


<a name="confirmcred" />

#### <a name="confirm-credential"></a>Conferma delle credenziali

L'utilizzo dei nuovi *conferma delle credenziali* funzionalità di Android Marshmallow, è possibile liberare gli utenti di dover ricordare e immettere le password di app specifiche mediante l'autenticazione in base ai quanto tempo il dispositivo è stato sbloccato.
A tale scopo, utilizzare il nuovo `SetUserAuthenticationValidityDurationSeconds` metodo il `KeyGenerator`. Utilizzare il `KeyGuardManager`del `CreateConfirmDeviceCredentialIntent` metodo per autenticare nuovamente l'utente all'interno dell'app. Per ulteriori informazioni su questa nuova funzionalità di Android Marshmallow, vedere [confermare credenziali](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin offre un'app di esempio che illustra come usare le credenziali di dispositivo (ad esempio PIN o password) nell'app: [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

Per usare questa app di esempio:

1.  Installazione di una schermata di blocco protetto sul dispositivo (**sicura > sicurezza > Screenlock**).
2.  Toccare il **acquisto** pulsante e verificare le credenziali di schermata di blocco protetto.


<a name="chrometabs" />

### <a name="chrome-custom-tabs"></a>Schede personalizzate Chrome

Gli sviluppatori di App devono affrontare una scelta quando un utente digita un URL: l'app può avviare un browser oppure usare un browser all'interno dell'applicazione in base a un `WebView`. Entrambe le opzioni pongono sfide &ndash; avviando il browser è un cambio di contesto eccessivo che non è personalizzabile, mentre `WebView`non condividano lo stato con il browser. Inoltre, utilizzare `WebView`s possibile aggiungere l'overhead di manutenzione aggiuntiva. 

*Chrome personalizzato schede* consente di visualizzare in modo semplice e richiede solo pochi siti Web con la potenza di Chrome senza lasciare l'app agli utenti. Questa funzionalità offre l'app più preciso l'esperienza dell'utente web; rende le transizioni tra nativo e il contenuto più trasparente web senza dover ricorrere a un `WebView`. L'app può influenzare come Chrome struttura e l'aspetto, personalizzare le operazioni seguenti: 

-   Colore della barra degli strumenti

-   Immettere e chiudere le animazioni

-   Azioni personalizzate nel menu della barra degli strumenti e l'overflow di Chrome

-   Chrome prelettura pre-inizio e di contenuto (per un caricamento più veloce)

Per sfruttare i vantaggi di questa funzionalità nell'app xamarin, scaricare e installare il [schede libreria personalizzati di Android supporto](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Per ulteriori informazioni su questa funzionalità, vedere [Chrome personalizzato schede](https://developer.chrome.com/multidevice/android/customtabs).


<a name="designlib" />

### <a name="material-design-support-library"></a>Libreria di supporto di materiale di progettazione

Simbolo Android introdotto [progettazione materiale](http://www.google.com/design/spec/material-design/introduction.html) come un nuovo linguaggio di progettazione per aggiornare l'esperienza Android (vedere [tema materiale](~/android/user-interface/material-theme.md) per informazioni sull'utilizzo di progettazione materiale in App xamarin). Con Android Marshmallow, Google è stata introdotta la *libreria di supporto di progettazione Android* per renderne più semplice per l'app agli sviluppatori di adottare materiale progettano l'aspetto. Questa raccolta include i componenti seguenti:

-   **CoordinatorLayout** &ndash; nuovo `CoordinatorLayout` widget è simile ma più potenti un `FrameLayout`. È possibile utilizzare `CoordinatorLayout` come un contenitore per le visualizzazioni figlio o un layout di primo livello che fornisce un `layout_anchor` attributo che può essere utilizzato per le visualizzazioni di ancoraggio relativo altre visualizzazioni.

-   **Barre degli strumenti di compressione** &ndash; nuovo `CollapsingToolbarLayout` è una barra app compressione che è un wrapper per `Toolbar`. (Si noti che il *barra app* è ciò che in precedenza è stata definita per il *barra delle azioni*.)

-   **Pulsante di azione mobile** &ndash; un pulsante circolare che indica l'azione principale nell'interfaccia dell'app.

-   **Mobile etichette per la modifica del testo** &ndash; utilizza un nuovo `TextInputLayout` widget (che esegue il wrapping `EditText`) per visualizzare un'etichetta a virgola mobile, quando un hint viene nascosto quando un utente immette un testo.

-   **Visualizzazione struttura** &ndash; nuovo `NavigationView` widget contribuisce di utilizzare il pannello di navigazione in un modo più semplice per gli utenti possono spostarsi.

-   **Snackbar** &ndash; nuovo `SnackBar` widget è un meccanismo di feedback lightweight (simile a un avviso popup) che consente di visualizzare un breve messaggio nella parte inferiore dello schermo, visualizzati sopra tutti gli altri elementi sullo schermo.

-   **Schede materiale** &ndash; nuovo `TabLayout` widget fornisce un layout orizzontale per visualizzare le schede come modalità per implementare lo spostamento di livello principale nell'app.

Per sfruttare il [libreria di supporto di progettazione](http://developer.android.com/tools/support-library/features.html#design) nell'app xamarin, scaricare e installare il Xamarin [progettazione libreria di supporto Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) pacchetto NuGet.

Vedere [efficaci progettazione materiale con la libreria di progettazione di supporto Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) per ulteriori informazioni (con esempi di codice) utilizzando la libreria di supporto di progettazione di materiale in App xamarin.
Xamarin offre un'app di esempio che demo nella nuova libreria di progettazione Android su xamarin &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare).
Questo esempio illustra le funzionalità della libreria di progettazione seguenti:


-   Compressione della barra degli strumenti
-   Pulsante di azione a virgola mobile
-   L'ancoraggio di visualizzazione
-   NavigationView
-   Snackbar

Per ulteriori informazioni sulla libreria di progettazione, vedere [libreria di supporto di progettazione Android](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) nel blog per sviluppatori Android.


<a name="libraries" />

### <a name="additional-library-updates"></a>Aggiornamenti di libreria aggiuntive

Oltre a Android Marshmallow, Google ha annunciato aggiornamenti correlati alle diverse librerie Android di core. Xamarin offre il supporto di xamarin per gli aggiornamenti tramite diversi pacchetti di NuGet versione di anteprima: 

-   [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; la versione più recente di Google Play Services include il nuovo *App invita* funzionalità che consente agli utenti di condividere la propria applicazione con gli amici. Per ulteriori informazioni su questa funzionalità, vedere [Reach espandere dell'applicazione con App invita di Google](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

-   [Librerie di supporto Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; NuGets questi offrono funzionalità che sono disponibili solo per API della libreria fornendo versioni di compatibilità di framework API Android. 

-   [Libreria indossabile Android](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; questo NuGet include associazioni di Google Play Services. La versione più recente della libreria indossabile offre nuove funzionalità (compresi una navigazione più semplice per le applicazioni personalizzate) per la piattaforma Android accenti. 


## <a name="summary"></a>Riepilogo

In questo articolo introdotto Android Marshmallow e viene illustrato come installare e configurare gli strumenti e i pacchetti per lo sviluppo di xamarin più recenti su Marshmallow. Inoltre, fornisce una panoramica delle nuove funzionalità di Android Marshmallow più interessanti per lo sviluppo di xamarin.


## <a name="related-links"></a>Collegamenti correlati

- [Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html)
- [Ottenere Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Cenni preliminari sulle funzionalità](https://developer.android.com/preview/api-overview.html)
- [Note sulla versione](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions (esempio)](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential (sample)](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog (esempio)](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
