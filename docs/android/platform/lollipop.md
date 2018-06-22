---
title: Funzionalità di simbolo
description: Questo articolo fornisce una panoramica delle nuove funzionalità introdotte in Android 5.0 (simbolo). Queste funzionalità includono un nuovo stile interfaccia utente chiamato tema materiale, nonché nuove funzionalità di supporto, quali animazioni, Visualizza ombreggiature e tinte drawable. Android 5.0 include anche le notifiche avanzate, due nuovi widget dell'interfaccia utente, una nuova pianificazione processo e un numero limitato di nuove API per migliorare l'archiviazione, rete, la connettività e funzionalità multimediali.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: cdef611525abbe4f066959c0ac56380b1c617747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773615"
---
# <a name="lollipop-features"></a>Funzionalità di simbolo

_Questo articolo fornisce una panoramica delle nuove funzionalità introdotte in Android 5.0 (simbolo). Queste funzionalità includono un nuovo stile interfaccia utente chiamato tema materiale, nonché nuove funzionalità di supporto, quali animazioni, Visualizza ombreggiature e tinte drawable. Android 5.0 include anche le notifiche avanzate, due nuovi widget dell'interfaccia utente, una nuova pianificazione processo e un numero limitato di nuove API per migliorare l'archiviazione, rete, la connettività e funzionalità multimediali._

## <a name="lollipop-overview"></a>Panoramica di simbolo

Android 5.0 (simbolo) introduce un nuovo linguaggio di progettazione, *progettazione materiale*, ed è un oggetto che supporta il cast delle nuove funzionalità per rendere le app semplici e intuitive per utilizzare. Con progettazione materiale, Android 5.0 non concede solo telefoni Android si rinnova; fornisce inoltre un nuovo set di regole di progettazione per i Tablet basati su Android, computer desktop, le espressioni di controllo e TV smart. Queste regole di progettazione accentuano semplicità e facile trovarvi rendendo utilizzare attributi tattili familiarità (ad esempio realistici segnali di area e bordo) per consentire agli utenti di rapidamente e comprendere in modo intuitivo l'interfaccia.

*Tema materiale* è la rappresentazione di tali principi di progettazione dell'interfaccia utente in Android. In questo articolo inizia con la copertura delle funzionalità di supporto del materiale tema:

-   **Le animazioni** &ndash; *tocco feedback* animazioni, *transizione attività* animazioni, *transizione dello stato di visualizzazione* animazioni e un *rivelare effetto*.

-   **Visualizzare shadows e l'elevazione dei privilegi** &ndash; viste è ora dispongono un `elevation` proprietà; le viste con maggiore `elevation` valori maggiori ombre sullo sfondo.

-   **Funzionalità di colore** &ndash; *tinte Drawable* rende possibile riutilizzare gli asset delle immagini modificando il colore e e *estrazione di colore significative* consente in modo dinamico tema dell'app dipende dai colori in un'immagine.

Tema di materiale molte funzionalità sono già incorporate in interfaccia utente Android 5.0 verificarsi, mentre altri devono essere aggiunti esplicitamente a app. Ad esempio, alcune visualizzazioni standard (ad esempio i pulsanti) include già le animazioni di commenti e suggerimenti tocco, mentre l'App è necessario abilitare la maggior parte delle ombreggiature di visualizzazione.

Oltre ai miglioramenti dell'interfaccia utente nasce tramite tema materiale, Android 5.0 include inoltre diverse altre nuove funzionalità che rientrano in questo articolo:

-   **Enhanced notifiche** &ndash; notifiche in Android 5.0 sono state aggiornate in modo significativo con un nuovo aspetto, il supporto per le notifiche lockscreen e un nuovo *Heads-up* il formato di presentazione di notifica.

-   **Nuovi widget dell'interfaccia utente** &ndash; nuovo `RecyclerView` widget rende più semplice per le app trasmettere grandi set di dati e le informazioni più complesse e la nuova `CardView` widget fornisce un formato di presentazione scheda semplificata per la visualizzazione di testo e immagini.

-   **Nuove API** &ndash; Android 5.0 aggiunge nuove API per il supporto di rete più, migliorata connettività Bluetooth, semplifica la gestione di archiviazione e un controllo più flessibile dei lettori multimediali e i dispositivi webcam. Un nuovo processo di pianificazione delle funzionalità è disponibile per l'esecuzione di attività in modo asincrono a intervalli pianificati. Questa funzionalità consente di migliorare la durata della batteria, ad esempio, la pianificazione di attività da eseguire sul posto quando il dispositivo è collegato e ad addebitare i costi.


## <a name="requirements"></a>Requisiti

Di seguito è necessario utilizzare le nuove funzionalità di Android 5.0 nelle App basate su Xamarin:

-   **Xamarin** &ndash; xamarin 4.20 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac. 

-   **Android SDK** &ndash; Android 5.0 (API 21) o versioni successive deve essere installato mediante Android SDK Manager.

-   **Java Developer Kit** &ndash; xamarin richiede [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se sviluppano applicazioni per il livello di API 24 o superiore (JDK 1.8 supporta inoltre API livelli precedenti a 24, compresi i simboli). La versione a 64 bit di JDK 1.8 è obbligatoria se si utilizza il Visualizzatore di form o di controlli personalizzati.

È possibile continuare a utilizzare [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) lo sviluppo in modo specifico per il livello API 23 o precedenti.


## <a name="setting-up-an-android-50-project"></a>Impostazione di un progetto Android 5.0

Per creare un progetto Android 5.0, è necessario installare il più recente degli strumenti e i pacchetti SDK. Utilizzare la procedura seguente per impostare un progetto xamarin che fa riferimento Android 5.0:

1. Installare gli strumenti di xamarin e attivare la licenza di Xamarin. Vedere [installazione e configurazione](~/android/get-started/installation/index.md) per ulteriori informazioni sull'installazione di xamarin.

2. Se si utilizza Visual Studio per Mac, installare gli ultimi aggiornamenti di Android 5.0.

3. Avviare Android SDK Manager (in Visual Studio per Mac, usare **strumenti &gt; Open Android SDK Manager&hellip;**) e installare strumenti di Android SDK 23.0.5 o versione successiva:

    [![Selezionare gli strumenti di Android SDK in Android SDK Manager](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Inoltre, installare i pacchetti Android 5.0 SDK più recenti (API 21 o versione successiva):

    [![Installazione dei pacchetti Android 5.0 SDK in Android SDK Manager](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Per ulteriori informazioni sull'utilizzo di Android SDK Manager, vedere [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).

4. Creare un nuovo progetto di xamarin. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per informazioni sulla creazione di progetti Android. Quando si crea un progetto Android, assicurarsi di configurare le impostazioni di versione per Android 5.0.
   In Visual Studio per Mac, passare a **opzioni progetto &gt; compilare &gt; generale** e impostare **framework di destinazione** a **Android 5.0 (simbolo)** o in un secondo momento:

    ![Impostazione di Framwework di destinazione per il simbolo Android 5.0](lollipop-images/target-framework.png)

   In **opzioni progetto &gt; compilare &gt; applicazione Android**, impostare minimo e di destinazione Android versione **automatico - versione di framework di destinazione utilizzare**:

    ![Impostare le versioni minima e di destinazione Android per automatico](lollipop-images/minimum-android-version.png)

5. Configurare un emulatore o un dispositivo Android per testare l'app. Se si utilizza un emulatore, vedere [il programma di installazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md) per informazioni su come configurare un emulatore Android per l'uso con Xamarin Studio o Visual Studio. Se si utilizza un dispositivo Android, vedere [impostazione backup Preview SDK](https://developer.android.com/preview/setup-sdk.html) per informazioni su come aggiornare il dispositivo per Android 5.0. Per configurare il dispositivo Android per l'esecuzione e il debug di applicazioni di xamarin, vedere [impostare backup dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Se si aggiorna un progetto Android esistente per l'anteprima L Android, è necessario aggiornare il **Framework di destinazione** e **Android versione** sui valori descritti sopra.

## <a name="important-changes"></a>Modifiche importanti

App Android pubblicate in precedenza potrebbero essere influenzate dalle modifiche apportate in Android 5.0. In particolare, Android 5.0 utilizza un nuovo runtime e un formato di notifica modificato in modo significativo.

### <a name="android-runtime"></a>Runtime Android

Android 5.0 usa il Runtime Android nuova (immagine) come il runtime predefinito anziché Dalvik. ClipArt implementa diverse nuove funzionalità importanti:

-   **Compilazione ahead di tempo (AOT)** &ndash; AOT può migliorare le prestazioni delle app per la compilazione del codice dell'app prima che l'app viene avviata prima. Quando si installa un'app, arte genera un'app compilata eseguibile per il dispositivo di destinazione.

-   **Migliorata l'operazione di garbage collection (GC)** &ndash; miglioramenti GC arte possono inoltre migliorare le prestazioni dell'applicazione. Operazione di Garbage collection ora utilizza pausa di un catalogo globale anziché due operazioni di Garbage Collection simultanee completare in modo più rapido.

-   **Debug di app migliorato** &ndash; arte fornisce informazioni dettagliate di diagnostica per facilitare l'analisi delle eccezioni e segnalazioni di arresto anomalo.

Le app di esistenti dovrebbero funzionare senza alcuna modifica in arte &ndash; ad eccezione delle applicazioni che sfruttano le tecniche univoche per il runtime Dalvik precedente, che potrebbero non funzionare in immagine. Per ulteriori informazioni su queste modifiche, vedere [verifica per determinare se un comportamento dell'App in fase di esecuzione Android (ART)](http://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Notifica modifiche

Le notifiche sono state modificate in modo significativo in Android 5.0:

-   **Suoni e alle vibrazioni vengono gestiti diversamente** &ndash; suoni notifica e alle vibrazioni vengono ora gestite da `Notification.Builder` anziché `Ringtone`, `MediaPlayer`, e `Vibrator`.

-   **Nuova combinazione di colori** &ndash; in base al tema di materiale, le notifiche vengono sottoposti a rendering con testo scuro su sfondo bianco o molto chiaro. Inoltre, è possibile modificare i canali alfa icone di notifica da Android per il coordinamento con combinazioni di colori di sistema. 

-   **Le notifiche Lockscreen** &ndash; notifiche possono ora essere visualizzati in lockscreen il dispositivo.

-   **Heads-up** &ndash; notifiche con priorità alta vengono ora visualizzati in una finestra mobile piccola (notifica Heads-up) quando il dispositivo sia sbloccato e la schermata viene attivata.

Nella maggior parte dei casi, il porting di funzionalità di notifica esistente app per Android 5.0 richiede i passaggi seguenti:

1.  Convertire il codice per utilizzare `Notification.Builder` (o `NotificationsCompat.Builder`) per la creazione di notifiche. 

2.  Verificare che le risorse di notifica esistente sono visibili nella nuova combinazione di colori del tema di materiale.

3.  Decidere quali visibilità devono avere le notifiche quando sono presentati nel lockscreen. Se una notifica non è pubblica, il contenuto che dovrebbe essere inclusa nel lockscreen?

4.  Impostare la categoria delle notifiche in modo che vengano gestiti correttamente il nuovo 5.0 Android *non disturbi* modalità.

Se le notifiche di controlli di trasporto, il supporto di visualizzazione dello stato della riproduzione, utilizzare `RemoteControlClient`, o chiamare `ActivityManager.GetRecentTasks`, vedere [importanti modifiche di comportamento](http://developer.android.com/preview/api-overview.html#Behaviors) per ulteriori informazioni sull'aggiornamento le notifiche per Android 5.0.

Per informazioni sulla creazione di notifiche in Android, vedere [notifiche locale](~/android/app-fundamentals/notifications/local-notifications.md). Il [compatibilità](~/android/app-fundamentals/notifications/local-notifications.md#compatibility) sezione di questo articolo viene illustrato come creare le notifiche che verso il basso compatibili con le versioni precedenti di Android.


## <a name="material-theme"></a>Tema materiale

Il nuovo tema di materiale Android 5.0 offre interventi di pulizia per l'aspetto dell'interfaccia utente di Android. Gli elementi visivi ora utilizzano superfici tattili che assumono la grafica grassetto tipografia e colori chiari di stampa. Esempi di materiale tema vengono rappresentati nelle schermate seguenti:

[![Schermate della schermata iniziale del tema materiale, schermata App e impostazione schermata](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 si riceveranno con la schermata iniziale visualizzata a sinistra. Nella schermata di centro è la prima schermata dell'elenco di app e la schermata a destra è la **impostazioni** dello schermo. Google [progettazione materiale](https://material.io/guidelines/material-design/introduction.html) specifica spiega le regole di progettazione sottostante si basa il nuovo concetto di materiale tema.

Tema materiale include tre caratteristiche incorporate che è possibile utilizzare nell'app: il `Theme.Material` tema scuro (impostazione predefinita), il `Theme.Material.Light` tema e `Theme.Material.Light.DarkActionBar` tema: 

[![Temi schermate scuro, chiaro e DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Per ulteriori informazioni sull'utilizzo delle funzionalità del tema di materiale in App xamarin, vedere [tema materiale](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animations

Android 5.0 fornisce tocco animazioni di commenti e suggerimenti, animazioni di transizione di attività e animazioni di transizione di stato di visualizzazione per rendere più intuitivo utilizzare interfacce di app. Inoltre, è possono usare le app Android 5.0 *rivelare effetto* animazioni per nascondere o visualizzare le visualizzazioni. È possibile utilizzare *curve movimento* le impostazioni per configurare la velocità o lenta animazioni vengono sottoposti a rendering.


### <a name="touch-feedback-animations"></a>Toccare le animazioni di commenti e suggerimenti

Tocco feedback animazioni forniscono agli utenti con un feedback visivo quando una vista è stata modificata. Ad esempio, i pulsanti ora visualizzare una reazione a quando sono interessate &ndash; tratta l'animazione di commenti e suggerimenti predefinito tocco Android 5.0. Animazione Ripple viene implementata dalla nuova `RippleDrawable` classe. L'effetto può essere configurato per terminare i limiti della vista o si estendono oltre i limiti della vista. Ad esempio, la sequenza di schermate seguente viene illustrato l'effetto di ripple in un pulsante durante l'animazione tocco:

![Frame da schermate di frame di animazione ripple su un pulsante](lollipop-images/touch-animation.png)

Contatto contatto iniziale con il pulsante si verifica nella prima immagine a sinistra, mentre la sequenza rimanente (da sinistra a destra) viene illustrato come l'effetto si estende fuori al bordo del pulsante. Quando termina l'animazione ripple, la vista restituisce l'aspetto originale. L'animazione ripple predefinita viene eseguita in una frazione di secondo, ma la lunghezza dell'animazione può essere personalizzata per superiori o inferiori periodi di tempo.

Per ulteriori informazioni su tocco animazioni di commenti e suggerimenti in Android 5.0, vedere [personalizzare Feedback tocco](http://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Attività animazioni di transizione

Animazioni di transizione attività agli utenti un'idea della continuità visual quando un'attività esegue la transizione a un altro. App è possono specificare tre tipi di animazioni di transizione:

-   **Immettere una transizione** &ndash; per quando un'attività immette la scena.

-   **Uscire dalla transizione** &ndash; per quando un'attività termina la scena.

-   **Elemento transizione condivisa** &ndash; per quando una vista che è comune a due attività Modifica il primo passaggio dell'attività al successivo.

Ad esempio, la sequenza di screenshot seguente illustra una transizione di un elemento condiviso:

[![Frame da schermate di frame di animazione di transizione un elemento condiviso](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento condiviso (una foto di un bruco) è una delle numerose visualizzazioni la prima attività; si ingrandisce affinché diventi la vista sola la seconda attività, il primo passaggio dell'attività al secondo.

#### <a name="enter-transition-animation-types"></a>Immettere i tipi di animazione di transizione

Per le transizioni di invio, Android 5.0 offre tre tipi di animazioni:

-   **Esplosione di animazione** &ndash; ingrandisce una visualizzazione dal centro della scena.

-   **Diapositiva animazione** &ndash; sposta una vista da uno dei lati di una scena.

-   **Animazione di dissolvenza** &ndash; dissolvenza una vista nella scena.

#### <a name="exit-transition-animation-types"></a>Tipi di animazione di transizione di uscita

Per le transizioni di uscita, Android 5.0 offre tre tipi di animazioni:

-   **Esplosione di animazione** &ndash; compatta una visualizzazione al centro della scena.

-   **Diapositiva animazione** &ndash; si sposta una vista su uno dei bordi della scena.

-   **Animazione di dissolvenza** &ndash; dissolvenza una vista fuori della scena.

#### <a name="shared-element-transition-animation-types"></a>Tipi di elemento transizione animazione condivisi

Elemento condiviso transizioni supportano più tipi di animazioni, ad esempio:

-   Modificare i limiti di layout o l'immagine di una vista.

-   Modifica la scala e la rotazione di una vista.

-   Modifica del tipo di dimensioni e la scala per una vista.

Per altre informazioni su animazioni di transizione di attività in Android 5.0, vedere [personalizzare transizioni attività](http://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animazioni di transizione dello stato di visualizzazione

Android 5.0 rende possibile per le animazioni da eseguire quando viene modificato lo stato di una vista. È possibile animare transizioni di stato di visualizzazione utilizzando una delle tecniche seguenti:

-   Creare drawables che animare le modifiche di state associate a una visualizzazione specifica. Il nuovo `AnimatedStateListDrawable` classe consente di creare drawables che consentono di visualizzare le animazioni tra dello stato di visualizzazione.

-   Definire le funzionalità di animazione che viene eseguito quando viene modificato lo stato di una vista. Il nuovo `StateListAnimator` classe consente di definire un animatore che viene eseguito quando viene modificato lo stato di una vista.

Per altre informazioni su animazioni di transizione di stato di visualizzazione in Android 5.0, vedere [animare modifiche allo stato di visualizzazione](http://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Mostra l'effetto

Il *rivelare effetto* è un cerchio di ritaglio radius che le modifiche per mostrare o nascondere una visualizzazione. È possibile controllare questo effetto impostando il raggio iniziale e finale del cerchio il ritaglio. La sequenza di schermate seguente viene illustrata un'animazione effetto rivelare dal centro dello schermo:

[![Schermate di frame di animazione di comparsa frame](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La sequenza successiva vengono illustrate un'animazione con effetto rivela che viene eseguita dall'angolo inferiore sinistro dello schermo:

[![Frame da schermate di frame di animazione di ritaglio](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Visualizzare le animazioni possano essere annullate; ovvero il cerchio di ritaglio possibile compattare per nascondere la visualizzazione anziché aumentare le dimensioni per visualizzare la vista.

Per ulteriori informazioni sull'effetto di rivelare Android 5.0 in, vedere [utilizzare l'effetto di rivelare](http://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Curva di movimento

Oltre a queste funzionalità di animazione, Android 5.0 fornisce inoltre nuove API che consentono di specificare le curve di tempo e il movimento delle animazioni. Android 5.0 utilizza queste curve per interpolare spostamento temporale e spaziale durante le animazioni. Tre curve sono definite in Android 5.0:

-   **Fast\_out\_lineare\_in** &ndash; accelera rapidamente e in continua crescita fino alla fine dell'animazione.

-   **Fast\_out\_lento\_in** &ndash; Accelerates rapidamente e lenta decelera verso la fine dell'animazione.

-   **Lineare\_out\_lento\_in** &ndash; inizia con una velocità di picco e lenta decelera, alla fine dell'animazione.

È possibile utilizzare il nuovo `PathInterpolator` classe per specificare come avviene interpolazione di movimento. `PathInterpolator` è un interpolatore che attraversa i percorsi animazione in base ai punti di controllo specificato e le curve di movimento. Per ulteriori informazioni su come specificare le impostazioni di movimento curvo in Android 5.0, vedere [movimento curvo utilizzare](http://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Visualizzazione Shadows il & elevazione

In Android 5.0, è possibile specificare il *elevazione* di una vista impostando un nuovo `Z` proprietà. Una maggiore `Z` valore determina la visualizzazione eseguire il cast di un'ombreggiatura più grande in background, visualizzando la visualizzazione per float maggiore rispetto allo sfondo. È possibile impostare l'elevazione iniziale di una vista tramite la configurazione relativa `elevation` attributo nel layout.

L'esempio seguente illustra le ombre create da un oggetto vuoto `TextView` controllare quando il relativo attributo di elevazione è impostato su 2dp 4dp e 6dp, rispettivamente:

[![Schermate di ombreggiature visualizzazione più grande progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Impostazioni di visualizzazione possono essere statiche (come illustrato in precedenza) oppure possono essere utilizzati nelle animazioni per rendere una vista per aumentare temporaneamente di sopra di sfondo della visualizzazione. È possibile utilizzare la `ViewPropertyAnimator` classe per aggiungere un'animazione per l'elevazione di una vista. L'elevazione di una vista è la somma del relativo layout `elevation` impostazione oltre a una `translationZ` proprietà che è possibile impostare tramite un `ViewPropertyAnimator` chiamata al metodo.

Per altre informazioni su shadows vista Android 5.0, vedere [Shadows definizione e le viste di ritaglio](http://developer.android.com/training/material/shadows-clipping.html).


## <a name="color-features"></a>Funzioni del colore

Android 5.0 fornisce due nuove funzionalità per la gestione di colore nelle App:

-   *Colorazione drawable* consente di modificare i colori degli asset immagine modificando un attributo di layout.

-   *Estrazione di colore significative* rende possibile personalizzare in modo dinamico il tema colori dell'applicazione per il coordinamento con la tavolozza dei colori di un'immagine visualizzata.


### <a name="drawable-tinting"></a>Colorazione drawable

Riconosce un nuovo 5.0 layout Android `tint` attributo che è possibile utilizzare per impostare il colore di drawables senza dover creare più versioni di queste risorse per la visualizzazione di colori diversi. Per utilizzare questa funzionalità, si definisce una bitmap come maschera alfa e utilizzare il `tint` attributo per definire il colore dell'asset. In questo modo è possibile creare asset di una sola volta e il colore del layout in modo che corrisponda al tema.

Nell'esempio seguente, una risorsa singola immagine &ndash; un logo bianco con uno sfondo trasparente &ndash; viene utilizzato per creare varianti tint:

![Logo di Xamarin vuoti con sfondo trasparente](lollipop-images/xamarin-logo-white.png)

Questo logo viene visualizzato sopra uno sfondo blu circolare, come illustrato negli esempi seguenti. L'immagine a sinistra è la modalità in cui viene visualizzato il logo senza un `tint` impostazione. Nel centro, il logo dell'immagine `tint` attributo è impostato su grigio scuro. Nell'immagine a destra, `tint` è impostata su un grigio chiaro:

![Esempi del logo con impostazioni diverse tint precedente](lollipop-images/drawable-tinting.png)

Per altre informazioni su drawable tinte Android 5.0, vedere [tinte Drawable](http://developer.android.com/training/material/drawables.html#DrawableTint).


### <a name="prominent-color-extraction"></a>Estrazione di colore significative

Il nuovo Android 5.0 `Palette` classe consente di estrarre i colori da un'immagine in modo che è possibile applicare in modo dinamico per una tavolozza colori personalizzata. La `Palette` classe estrae i sei colori da un'immagine e i colori in base ai relativi livelli relativi di saturazione e luminosità delle etichette:

-   Vivace

-   Vivace scuro

-   Vivace light

-   Disattivato

-   Scuro audio disattivato

-   Luce audio disattivato

Nelle schermate seguenti, ad esempio, una foto Visualizza app estrae i colori principali dall'immagine sullo schermo e Usa i colori per adattare la combinazione di colori dell'applicazione corrisponda all'immagine:

[![Schermate di estrazioni di colore verde, blu e rosa tema](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Nelle schermate precedenti, la barra delle azioni è impostata su "light di vivace" estratti colore e lo sfondo è impostato su "dark di vivace" estratti colore. In ogni esempio precedente, una riga dei quadrati del colore viene inclusa per illustrare i colori della tavolozza che sono stati estratti dall'immagine.

Per ulteriori informazioni sull'estrazione di colore in Android 5.0, vedere [estrazione colori principali da un'immagine](http://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Nuovi widget dell'interfaccia utente

Android 5.0 sono stati introdotti due nuovi widget dell'interfaccia utente:

-   `RecyclerView` &ndash; Un gruppo di visualizzazione che consente di visualizzare un elenco di elementi scorrevoli.

-   `CardView` &ndash; Un layout di base con angoli arrotondati.

Entrambi widget includono il supporto baked-in per le funzionalità del tema materiale; ad esempio, `RecyclerView` Usa animazioni per l'aggiunta e rimozione di viste, e `CardView` utilizza Visualizza shadows per ciascuna scheda mobile di sopra di background. Nelle schermate seguenti sono illustrati esempi di questi nuovi widget:

[![Schermate dell'App compilata con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Nella schermata di sinistra è un esempio di `RecyclerView` utilizzato in un'app di posta elettronica e la schermata in destra è un esempio di `CardView` utilizzato in un'applicazione di prenotazione di viaggio.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` è simile a `ListView,` ma se è più adatto per grandi set di viste o elenchi con gli elementi che cambiano in modo dinamico. Ad esempio `ListView,` è specificare un adattatore per accedere al set di dati sottostante. Tuttavia, a differenza di `ListView,` si utilizza un *manager layout* per posizionare gli elementi all'interno di `RecyclerView`. Gestore del layout anche si occupa della visualizzazione riciclo; gestisce il riutilizzo delle viste di elemento che non sono più visibili all'utente.

Quando si utilizza un `RecyclerView` widget, è necessario specificare un `LayoutManager` e un adapter. Come illustrato nella figura, `LayoutManager` è intermediario tra l'adapter e `RecyclerView`:

![Diagramma di RecyclerView con supporto LayoutManager, Adapter e set di dati](lollipop-images/recyclerview-diagram.png)

La schermata seguente viene illustrato un `RecyclerView` che contiene 100 elementi (ogni elemento è costituito da un `ImageView` e `TextView`):

[![Schermate di un'app RecyclerView scorrimento di immagini](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` gestisce il set di dati di grandi dimensioni con facilità &ndash; lo scorrimento dall'inizio dell'elenco alla fine dell'elenco in questo esempio app richiede solo pochi secondi. `RecyclerView` supporta inoltre le animazioni; in effetti, le animazioni per l'aggiunta e rimozione di elementi sono attivate per impostazione predefinita. Quando un elemento viene aggiunto a un `RecyclerView`, di dissolvenza in, come illustrato in questa sequenza di schermate:

[![Schermata di frame di una dissolvenza elemento foto in frame](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Per altre informazioni su `RecyclerView`, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>CardView

`CardView` è una visualizzazione semplice che simula una scheda a virgola mobile con angoli arrotondati. Poiché `CardView` ha shadows visualizzazione incorporata, offre un modo semplice per aggiungere profondità all'app. Le schermate seguenti mostrano tre esempi di orientati al testo di `CardView`:

[![Schermate di esempio di App usando RecyclerView con gli elementi basati su CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Ognuna delle schede nell'esempio precedente contiene un `TextView`; il colore di sfondo è impostato tramite il `cardBackgroundColor` attributo.

Per altre informazioni su `CardView`, vedere [CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notifiche avanzate

Il sistema di notifica in Android 5.0 è stato aggiornato in modo significativo con nuove funzionalità e un nuovo formato visivo. Le notifiche di disporre di un nuovo aspetto in Android 5.0. Ad esempio, le notifiche in Android 5.0 ora usare testo scuro su uno sfondo:

![Esempio di una notifica di Android 5.0 non espansa](lollipop-images/expanded-notification-contracted.png)

Quando viene visualizzata un'icona di grandi dimensioni in una notifica (come illustrato nell'esempio precedente), dell'icona piccola in Android 5.0 viene presentata come un badge sull'icona di grandi dimensioni. 

In Android 5.0, le notifiche possono essere inoltre visualizzate lockscreen il dispositivo.
Ad esempio, ecco una schermata di esempio di un lockscreen con una singola notifica:

[![Schermata di notifica visualizzato nella schermata di blocco](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Gli utenti possono toccare due volte una notifica sul lockscreen sbloccare il dispositivo e passare all'app che ha avuto origine la notifica, oppure scorrere rapidamente per chiudere la notifica. Le notifiche sono una nuova *visibilità* l'impostazione che determina la quantità di contenuto possono essere visualizzati di lockscreen. Gli utenti possono scegliere se consentire contenuto riservato da visualizzare nelle notifiche lockscreen.

Android 5.0 introduce un nuovo formato di presentazione di notifica ad alta priorità denominato *Heads-up*. Le notifiche Heads-up scorrere verso il basso dalla parte superiore dello schermo per pochi secondi e quindi riesame nuovamente per la sfumatura di notifica nella parte superiore della schermata. Le notifiche Heads-up rendono possibile per il sistema dell'interfaccia utente di inserire informazioni importanti davanti all'utente senza interrompere l'attività attualmente in esecuzione. L'esempio seguente illustra una notifica Heads-up semplice che consente di visualizzare nella parte superiore di un'app:

[![Esempio di una notifica heads-up](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Le notifiche di Heads-up in genere vengono usate per gli eventi seguenti:

-   Un nuovo messaggio successivo

-   Una chiamata telefonica in ingresso

-   Indicazione di batteria in esaurimento

-   Un allarme

Android 5.0 consente di visualizzare una notifica in formato Heads-up solo quando dispone di un'impostazione di priorità alta o max.

In Android 5.0, è possibile fornire i metadati di notifica per ordinare e visualizzare più in modo intelligente le notifiche Android. Android 5.0 consente di organizzare le notifiche in base alla priorità, visibilità e la categoria.
Le categorie di notifica vengono utilizzate per filtrare le notifiche che possono essere presentate quando il dispositivo è in *non disturbi* modalità.

Per informazioni dettagliate sulla creazione e l'avvio di notifiche con le funzionalità più recenti di Android 5.0, vedere [notifiche locale](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Nuove API

Oltre alle nuove funzionalità e aspetto descritte sopra, Android 5.0 aggiunge nuove API che estendono la funzionalità degli elementi multimediali esistenti, archiviazione e funzionalità e di connettività wireless. Inoltre, Android 5.0 include nuove API che forniscono supporto per una nuova funzionalità di utilità di pianificazione di processo.

### <a name="camera"></a>Fotocamera

Android 5.0 fornisce diverse nuove API per la funzionalità avanzata della fotocamera. Il nuovo `Android.Hardware.Camera2` spazio dei nomi include funzionalità per l'accesso a singoli fotocamere connessi a un dispositivo Android. Inoltre, `Android.Hardware.Camera2` ogni fotocamera come una pipeline di modelli: accetta una richiesta di acquisizione, acquisisce l'immagine e quindi restituisce il risultato. Questo approccio rende possibile per le app di accodare più richieste di acquisizione di una fotocamera.

Le seguenti API consentono queste nuove funzionalità:

-   `CameraManager.GetCameraIdList` &ndash; Consente di accedere a livello di codice i dispositivi webcam; Utilizzare `CameraManager.OpenCamera` per connettersi a un dispositivo specifico della fotocamera.

-   `CameraCaptureSession` &ndash; Acquisisce o crea un flusso di immagini da fotocamera. Si implementa un `CameraCaptureSession.CaptureListener` interfaccia per gestire i nuovi eventi di acquisizione immagine.

-   `CaptureRequest` &ndash; Definisce i parametri di acquisizione.

-   `CaptureResult` &ndash; Fornisce i risultati di un'operazione di acquisizione immagine.

Per ulteriori informazioni relative alla fotocamera nuove API di Android 5.0, vedere [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Audio Playback

5.0 aggiornamenti Android la `AudioTrack` classe per una migliore riproduzione audio:

-   `ENCODING_PCM_FLOAT` &ndash; Configura `AudioTrack` per accettare dati audio nel formato a virgola mobile per una migliore intervallo dinamico, maggiore capacità aggiuntiva e una qualità più elevata (grazie a una maggiore precisione). Inoltre, il formato a virgola mobile consente di evitare il ritaglio di audio.

-   `ByteBuffer` &ndash; È possibile specificare i dati audio per `AudioTrack` come una matrice di byte.

-   `WRITE_NON_BLOCKING` &ndash; Questa opzione consente di semplificare la memorizzazione nel buffer e multithreading per alcune applicazioni.

Per altre informazioni su `AudioTrack` miglioramenti in Android 5.0, vedere [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controllo della riproduzione multimediale

Android 5.0 introduce il nuovo `Android.Media.MediaController` classe, che sostituisce `RemoteControlClient`. `Android.Media.MediaController` fornisce un controllo API semplificata di trasporto e offre un controllo di thread-safe di riproduzione all'esterno del contesto dell'interfaccia utente. Le seguenti nuove API di gestire il controllo di trasporto:

-   `Android.Media.Session.MediaSession` &ndash; Una sessione di controllo di supporto che gestisce più controller. Si chiama `MediaSession.GetSessionToken` per richiedere un token che l'app Usa per interagire con la sessione.

-   `MediaController.TransportControls` &ndash; Handle di trasporto, ad esempio i comandi **riprodurre**, **arrestare**, e **Skip**.

Inoltre, è possibile utilizzare il nuovo `Android.App.Notification.MediaStyle` classe da associare a una sessione di supporto con il contenuto RTF notifica (ad esempio l'estrazione e visualizzazione di un album).

Per altre informazioni sulle nuove funzionalità di controllo supporti la riproduzione di Android 5.0, vedere [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Archiviazione

Android 5.0 aggiorna il Framework di accesso di archiviazione per rendere più semplice per lavorare con documenti e le directory delle applicazioni:

-   Per selezionare un sottoalbero di directory, è possibile creare e inviare un `Android.Intent.Action.OPEN_DOCUMENT_TREE` finalità. Questa finalità fa sì che il sistema visualizzare tutte le istanze del provider che supportano la selezione sottoalbero; l'utente cerca e seleziona una directory.

-   Per creare e gestire nuovi documenti o directory in un punto qualsiasi in un sottoalbero, utilizzare il nuovo `CreateDocument`, `RenameDocument`, e `DeleteDocument` metodi di `DocumentsContract`.

-   Per ottenere i percorsi di directory supporti su tutti i dispositivi di archiviazione condivisa, si chiama il nuovo `Android.Content.Context.GetExternalMediaDirs` metodo.

Per ulteriori informazioni sulla nuova risorsa di archiviazione API Android 5.0, vedere [archiviazione](http://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Connettività e wireless

Android 5.0 aggiunge i seguenti miglioramenti di API per la connettività e wireless:

-   Nuovo *multi-rete* API che rendono possibile per le app trovare e selezionare le reti con funzionalità specifiche prima di effettuare una connessione.

-   Funzionalità di trasmissione Bluetooth che consente a un dispositivo Android 5.0 come una periferica Bluetooth debole energia.

-   Miglioramenti di NFC che semplificano l'utilizzo delle funzionalità di comunicazione di prossimità di campo per condividere dati con gli altri dispositivi.

Per ulteriori informazioni sulla nuova wireless e connettività API Android 5.0, vedere [Wireless e connettività](http://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Pianificazione dei processi

Android 5.0 introduce un nuovo `JobScheduler` API che consente agli utenti di ridurre al minimo della batteria pianificando determinate attività venga eseguita solo quando il dispositivo sia connesso e ad addebitare i costi. Questa funzionalità di utilità di pianificazione del processo nonché per la pianificazione di un'attività da eseguire quando le condizioni sono più adatti a tale attività, ad esempio quando il dispositivo è connesso attraverso una rete Wi-Fi anziché a una rete a consumo per scaricare un file di grandi dimensioni.

Per ulteriori informazioni sul processo nuova pianificazione API Android 5.0, vedere [pianificazione di processi](http://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Riepilogo

In questo articolo è fornita una panoramica delle nuove importanti funzionalità di Android 5.0 per gli sviluppatori di app xamarin:

-   Tema materiale

-   Animations

-   Shadows di visualizzazione e l'elevazione dei privilegi

-   Funzionalità di colore, ad esempio la colorazione drawable e significative colore estrazione

-   Il nuovo `RecyclerView` e `CardView` widget

-   Miglioramenti di notifica

-   Nuove API per fotocamera riproduzione audio, controllo media, archiviazione, e di connettività wireless e pianificazione dei processi

Se ha esperienza di sviluppo di App Android di Xamarin, leggere [installazione e configurazione](~/android/get-started/installation/index.md) consentono di introduzione a xamarin.
[Hello, Android](~/android/get-started/hello-android/index.md) viene fornita un'introduzione eccellente per imparare a creare i progetti Android.



## <a name="related-links"></a>Collegamenti correlati

- [L Android Developer Preview](http://developer.android.com/preview/index.html)
- [Ottenere Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Progettazione materiale](http://developer.android.com/preview/material/index.html)
- [Principi di progettazione materiale](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
