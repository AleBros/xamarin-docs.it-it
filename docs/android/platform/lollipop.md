---
title: Funzionalità di lollipop
description: Questo articolo fornisce una panoramica generale delle nuove funzionalità introdotte in Android 5.0 (Lollipop). Queste funzionalità includono un nuovo stile di interfaccia utente denominato tema Material, nonché nuove funzionalità di supporta, ad esempio le animazioni, Visualizza ombreggiature e tinte drawable. Android 5.0 include anche le notifiche migliorate, due nuovi widget dell'interfaccia utente, una nuova utilità di pianificazione di processi e un numero limitato di nuove API per migliorare l'archiviazione, rete, la connettività e funzionalità multimediali.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d79c0563d1dc9a2cfe75b702300982bb4d38553b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117863"
---
# <a name="lollipop-features"></a>Funzionalità di lollipop

_Questo articolo fornisce una panoramica generale delle nuove funzionalità introdotte in Android 5.0 (Lollipop). Queste funzionalità includono un nuovo stile di interfaccia utente denominato tema Material, nonché nuove funzionalità di supporta, ad esempio le animazioni, Visualizza ombreggiature e tinte drawable. Android 5.0 include anche le notifiche migliorate, due nuovi widget dell'interfaccia utente, una nuova utilità di pianificazione di processi e un numero limitato di nuove API per migliorare l'archiviazione, rete, la connettività e funzionalità multimediali._

## <a name="lollipop-overview"></a>Panoramica di lollipop

Android 5.0 (Lollipop) introduce un nuovo linguaggio di progettazione *Material Design*, e con esso un oggetto che supporta il cast delle nuove funzionalità per rendere le app più facile e intuitivo da utilizzare. Con la progettazione di materiale, Android 5.0 non solo offre telefoni Android si rinnova; fornisce inoltre un nuovo set di regole di progettazione per Tablet basati su Android, computer desktop, le espressioni di controllo e smart TV. Queste regole di progettazione evidenziano la semplicità e facile trovarvi mentre si usano attributi tattili familiare (ad esempio realistici segnali di area e bordo) per consentire agli utenti rapidamente e comprende intuitivamente l'interfaccia.

*Tema Material* è la rappresentazione di tali principi di progettazione dell'interfaccia utente in Android. Questo articolo ha inizio in cui vengono trattate le funzionalità di supporto del materiale tema:

-   **Le animazioni** &ndash; *Touch commenti e suggerimenti* dalle animazioni fisiche, *transizione attività* dalle animazioni fisiche, *consente di visualizzare la transizione dello stato* animazioni e un *rivelare effetto*.

-   **Visualizza ombreggiature e l'elevazione dei privilegi** &ndash; le visualizzazioni hanno ora un' `elevation` proprietà; viste con maggiore `elevation` valori più grandi ombre sullo sfondo.

-   **Le funzionalità dei colori** &ndash; *tinte Drawable* rende possibile riutilizzare gli asset delle immagini modificando il colore, e *estrazione colore notificate all'utente* consente in modo dinamico tema all'app in base i colori in un'immagine.

Tema di materiale di molte funzionalità sono già integrate di Android 5.0 relativa interfaccia utente, mentre altri devono essere aggiunti esplicitamente all'app. Ad esempio, alcune visualizzazioni standard (ad esempio, i pulsanti) include già le animazioni di commenti e suggerimenti di tocco, mentre l'app deve abilitare la maggior parte delle ombreggiature di visualizzazione.

Oltre ai miglioramenti dell'interfaccia utente ha inventato tramite tema Material, Android 5.0 include anche diverse altre nuove funzionalità incluse in questo articolo:

-   **Notifiche avanzate** &ndash; le notifiche in Android 5.0 sono state aggiornate in modo significativo con un nuovo aspetto, il supporto per le notifiche schermata di blocco e una nuova *Heads-up* formato di presentazione di notifica.

-   **Nuovi widget dell'interfaccia utente** &ndash; nuova `RecyclerView` widget rende più semplice per le app trasmettere grandi set di dati e le informazioni più complesse e la nuova `CardView` widget fornisce un formato di presentazione in stile card semplificata per la visualizzazione di testo e immagini.

-   **Nuove API** &ndash; Android 5.0 aggiunge nuove API per il supporto di rete più migliorata connettività Bluetooth, semplifica la gestione di archiviazione e un controllo più flessibile di lettori multimediali e i dispositivi webcam. Un nuovo processo, la funzionalità di pianificazione è disponibile per eseguire le attività in modo asincrono all'ora pianificata. Questa funzionalità consente di migliorare la durata della batteria, ad esempio, la pianificazione di attività da eseguire sul posto quando il dispositivo sia connesso e l'addebito.


## <a name="requirements"></a>Requisiti

Per utilizzare le nuove funzionalità di Android 5.0 nelle App basate su Xamarin è necessario quanto segue:

-   **Xamarin. Android** &ndash; xamarin. Android 4.20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac. 

-   **Android SDK** &ndash; Android 5.0 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

-   **Java Developer Kit** &ndash; xamarin. Android richiede [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sviluppa per il livello API 24 o versione successiva (JDK 1.8 supporta anche i livelli API precedenti a 24, tra cui Lollipop). Se si usa il Visualizzatore anteprima del form o controlli personalizzati, è necessaria la versione a 64 bit di JDK 1.8.

È possibile continuare a usare [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si è lo sviluppo in modo specifico per il livello API 23 o versioni precedenti.


## <a name="setting-up-an-android-50-project"></a>Configurazione di un progetto Android 5.0

Per creare un progetto Android 5.0, è necessario installare gli strumenti più recenti e i pacchetti SDK. Usare la procedura seguente per configurare un progetto xamarin. Android destinata a Android 5.0:

1. Installare gli strumenti di xamarin. Android e attivare la licenza di Xamarin. Visualizzare [installazione e configurazione](~/android/get-started/installation/index.md) per altre informazioni sull'installazione di xamarin. Android.

2. Se si usa Visual Studio per Mac, installare gli aggiornamenti più recenti di Android 5.0.

3. Avviare Android SDK Manager (in Visual Studio per Mac, usare **degli strumenti &gt; Open Android SDK Manager&hellip;**) e installare Android SDK Tools 23.0.5 o versione successiva:

    [![Selezione di Android SDK tools di Android SDK Manager](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Inoltre, installare i pacchetti più recenti di Android 5.0 SDK (API 21 o versione successiva):

    [![Installazione dei pacchetti Android SDK 5.0 in Android SDK Manager](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Per altre informazioni sull'uso di Android SDK Manager, vedere [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).

4. Creare un nuovo progetto xamarin. Android. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti di Android. Quando si crea un progetto Android, assicurarsi di configurare le impostazioni di versione per Android 5.0.
   In Visual Studio per Mac, passare a **opzioni progetto &gt; compilare &gt; generali** e impostare **framework di destinazione** al **Android 5.0 (Lollipop)** o in un secondo momento:

    ![Se si imposta la destinazione Framwework su Android 5.0 Lollipop](lollipop-images/target-framework.png)

   Sotto **opzioni progetto &gt; compilare &gt; applicazione Android**, impostare minimo e versione Android di destinazione **automatico - versione di framework di destinazione usare**:

    ![Impostare le versioni minima e di destinazione Android su automatico](lollipop-images/minimum-android-version.png)

5. Configurare un emulatore o un dispositivo Android per testare l'app. Se si usa un emulatore, vedere [configurazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md) per informazioni su come configurare un emulatore Android per l'uso con Xamarin Studio o Visual Studio. Se si usa un dispositivo Android, vedere [impostazione di anteprima di SDK](https://developer.android.com/preview/setup-sdk.html) per informazioni su come aggiornare il dispositivo per Android 5.0. Per configurare il dispositivo Android per l'esecuzione e debug delle applicazioni xamarin. Android, vedere [configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Se si aggiorna un progetto Android esistente che era destinata all'anteprima L Android, è necessario aggiornare il **Framework di destinazione** e **Android versione** sui valori descritti in precedenza.

## <a name="important-changes"></a>Modifiche importanti

Le App per Android pubblicate in precedenza può esserne influenzate dalle modifiche apportate in Android 5.0. In particolare, Android 5.0 usa un nuovo runtime e un formato di notifica notevolmente modificati.

### <a name="android-runtime"></a>Runtime di Android

Android 5.0 usa il Runtime di Android nuovi (ART) come il runtime predefinito anziché Dalvik. ARTE implementa diverse nuove funzionalità importanti:

-   **La compilazione ahead of time (AOT)** &ndash; AOT può migliorare le prestazioni dell'app eseguendo la compilazione di codice dell'app prima che l'app viene avviata prima di tutto. Quando viene installata un'app, ART genera un eseguibile per il dispositivo di destinazione dell'app compilata.

-   **Migliorata l'operazione di garbage collection (GC)** &ndash; miglioramenti di Garbage Collection in ART possono anche migliorare le prestazioni delle app. Operazione di Garbage collection ora Usa una pausa GC anziché due e completare le operazioni di Garbage Collection simultanee in tempi più brevi.

-   **Debug delle app migliorato** &ndash; ART fornisce informazioni dettagliate di diagnostica per facilitare l'analisi delle eccezioni e segnalazioni di arresto anomalo.

Le app esistenti dovrebbero funzionare senza modifiche in ART &ndash; fatta eccezione per le app che sfruttano le tecniche univoche per il runtime Dalvik precedente, che potrebbe non funzionare in arte. Per altre informazioni su queste modifiche, vedere [verifica per determinare se un comportamento dell'App in fase di esecuzione Android (ART)](http://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Notifica delle modifiche

Le notifiche sono state modificate in modo significativo in Android 5.0:

-   **Suoni e vibrazione vengono gestiti diversamente** &ndash; suoni notifica e alle vibrazioni vengono ora gestite da `Notification.Builder` anziché `Ringtone`, `MediaPlayer`, e `Vibrator`.

-   **Nuova combinazione di colori** &ndash; in conformità con tema Material, le notifiche vengono visualizzate con testo scuro su sfondo bianco o molto leggero. Inoltre, è possibile modificare i canali alfa nelle icone di notifica da Android per il coordinamento con combinazioni di colori di sistema. 

-   **Le notifiche schermata di blocco** &ndash; le notifiche possono ora vengono visualizzate nella schermata di blocco dispositivi.

-   **Heads-up** &ndash; notifiche ad alta priorità vengono ora visualizzati in una piccola finestra mobile (notifica Heads-up) quando il dispositivo è sbloccato e la schermata è attivata.

Nella maggior parte dei casi, il porting di funzionalità di notifica app esistenti per Android 5.0, sono necessari i passaggi seguenti:

1.  Convertire il codice per utilizzare `Notification.Builder` (o `NotificationsCompat.Builder`) per la creazione di notifiche. 

2.  Verificare che le risorse di notifica esistenti vengono visualizzate nella nuova combinazione colori del tema Material.

3.  Decidere quali visibilità devono avere le notifiche quando sono presentati nella schermata di blocco. Se una notifica non è pubblica, il tipo di contenuto dovrebbe essere inclusa nella schermata di blocco?

4.  Impostare la categoria delle notifiche in modo che vengano gestiti correttamente di nuovo Android 5.0 *non disturbare* modalità.

Se le notifiche presentano controlli di trasporto, supporto di visualizzazione dello stato di riproduzione, utilizzare `RemoteControlClient`, oppure chiamare `ActivityManager.GetRecentTasks`, vedere [importanti modifiche di comportamento](http://developer.android.com/preview/api-overview.html#Behaviors) per altre informazioni sull'aggiornamento delle notifiche per Android 5.0.

Per informazioni sulla creazione di notifiche in Android, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md). Il [compatibilità](~/android/app-fundamentals/notifications/local-notifications.md#compatibility) sezione di questo articolo viene illustrato come creare le notifiche che sono compatibili con verso il basso con le versioni precedenti di Android.


## <a name="material-theme"></a>Tema Material

Il nuovo tema del materiale Android 5.0 introduce modifiche sostanziali per l'aspetto dell'interfaccia utente di Android. Gli elementi visivi ora usano tattili superfici che acquisiscano la grafica in grassetto, tipografia e colori di stampa. Esempi di tema Material sono rappresentati negli screenshot seguenti:

[![Screenshot della schermata iniziale del tema Material, schermata App e schermata di impostazione](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 saluta con la schermata iniziale visualizzata a sinistra. Nella schermata di center è la prima schermata dell'elenco di app e nello screenshot a destra è il **impostazioni** dello schermo. Google [Material Design](https://material.io/guidelines/material-design/introduction.html) specifica vengono illustrate le regole di progettazione sottostante si basa il nuovo concetto di tema Material.

Tema Material include tre versioni predefinite che è possibile usare nell'app: la `Theme.Material` tema scuro (impostazione predefinita), il `Theme.Material.Light` tema e il `Theme.Material.Light.DarkActionBar` tema: 

[![Screenshot di scuro, chiaro e DarkActionBar temi](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Per altre informazioni sull'uso delle funzionalità di Material tema nelle App xamarin. Android, vedere [tema Material](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animations

Android 5.0 fornisce le animazioni di commenti e suggerimenti di tocco, animazioni di transizione di attività e animazioni di transizione di stato di visualizzazione per rendere più intuitivo da utilizzare le interfacce app. Inoltre, è possono usare le app Android 5.0 *rivelare effetto* animazioni per nascondere o visualizzare le visualizzazioni. È possibile usare *curvi movimento* impostazioni per configurare la velocità o lentamente le animazioni vengono sottoposti a rendering.


### <a name="touch-feedback-animations"></a>Animazioni di commenti e suggerimenti di tocco

Le animazioni di commenti e suggerimenti di tocco offrono agli utenti un feedback visivo quando una visualizzazione è stata toccata. Ad esempio, i pulsanti a questo punto visualizzare un effetto domino quando vengono toccate &ndash; tratta l'animazione di commenti e suggerimenti predefinito tocco in Android 5.0. Animazione di Ripple viene implementata dal nuovo `RippleDrawable` classe. L'effetto domino può essere configurato per terminare in corrispondenza i limiti della visualizzazione o si estendono oltre i limiti della visualizzazione. Ad esempio, la sequenza di screenshot seguente illustra l'effetto domino in un pulsante durante l'animazione di tocco:

![Frame negli screenshot di frame di animazione di ripple su un pulsante](lollipop-images/touch-animation.png)

Contatto di tocco iniziale con il pulsante si verifica nella prima immagine a sinistra, mentre la sequenza rimanente (da sinistra a destra) viene illustrato come l'effetto domino diluire al bordo del pulsante. Quando termina l'animazione di ripple, la vista restituisce l'aspetto originale. L'animazione di ripple predefinita viene eseguita in una frazione di secondo, ma la lunghezza dell'animazione può essere personalizzata per più breve o più periodi di tempo.

Per altre informazioni su touch le animazioni di commenti e suggerimenti in Android 5.0, vedere [personalizzare il Feedback Touch](http://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Animazioni di transizione attività

Animazioni di transizione attività offrono agli utenti un senso di continuità aziendale visual quando un'attività esegue la transizione a un altro. Le app è possono specificare tre tipi di animazioni di transizione:

-   **Immettere transizione** &ndash; per quando un'attività entra in scena.

-   **Uscire dalla transizione** &ndash; per quando un'attività termina la scena.

-   **Transizione elemento condiviso** &ndash; per quando viene modificato una vista che è comune a due attività come le transizioni di attività prima a quella successiva.

Ad esempio, la sequenza di screenshot seguente illustra una transizione condivisa elemento:

[![Frame negli screenshot di frame di animazione di transizione condivisa elemento](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento condiviso (una foto di un bruco) è una delle diverse viste nella prima attività; si ingrandisce per diventare la visualizzazione solo nella seconda attività come le transizioni di attività prima alla seconda.

#### <a name="enter-transition-animation-types"></a>Immettere i tipi di animazione di transizione

Per le transizioni di invio, Android 5.0 fornisce tre tipi di animazioni:

-   **Explode animazione** &ndash; ingrandisce una visualizzazione dal centro della scena.

-   **Animazione di diapositive** &ndash; sposta una visualizzazione da uno dei bordi di una scena.

-   **Animazione di dissolvenza** &ndash; si dissolve un quadro della scena.

#### <a name="exit-transition-animation-types"></a>Tipi di animazione di transizione di uscita

Per le transizioni di uscita, Android 5.0 fornisce tre tipi di animazioni:

-   **Explode animazione** &ndash; compatta una visualizzazione al centro della scena.

-   **Animazione di diapositive** &ndash; si sposta una visualizzazione su uno dei bordi di una scena.

-   **Animazione di dissolvenza** &ndash; si dissolve una vista all'esterno della scena.

#### <a name="shared-element-transition-animation-types"></a>Tipi di animazione di transizione elemento condiviso

Elemento condiviso transizioni supportano più tipi di animazioni, ad esempio:

-   Modificare i limiti di layout o l'immagine di una vista.

-   Modifica la scala e la rotazione di una vista.

-   Modifica del tipo di ridimensionamento e scalabilità per una visualizzazione.

Per altre informazioni sulle animazioni di transizione di attività in Android 5.0, vedere [personalizzare le transizioni attività](http://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animazioni di transizione dello stato di visualizzazione

Android 5.0 rende possibile per le animazioni da eseguire quando cambia lo stato di una vista. È possibile animare le transizioni di stato di visualizzazione usando una delle tecniche seguenti:

-   Creare drawable che animare le modifiche di state associate a una visualizzazione specifica. Il nuovo `AnimatedStateListDrawable` classe consente di creare drawable che consentono di visualizzare le animazioni tra le modifiche dello stato di visualizzazione.

-   Definire le funzionalità di animazione che viene eseguita quando cambia lo stato di una vista. Il nuovo `StateListAnimator` classe consente di definire un animatore che viene eseguita quando cambia lo stato di una vista.

Per altre informazioni sulle animazioni di transizione di stato di visualizzazione in Android 5.0, vedere [animare modifiche di stato di visualizzazione](http://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Visualizzare l'effetto

Il *rivelare effetto* è un cerchio di ritaglio radius che le modifiche per mostrare o nascondere una vista. È possibile controllare questo effetto impostando i raggi iniziali e finali del cerchio ritaglio. La sequenza di screenshot seguente illustra un'animazione con effetto rivelare dal centro della schermata:

[![Frame negli screenshot di frame di animazione rivelare](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La sequenza successiva illustra un'animazione con effetto rivela che avviene dall'angolo inferiore sinistro della schermata:

[![Frame negli screenshot di frame di animazione di ritaglio](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Rivelare le animazioni possono essere annullate; vale a dire il cerchio di ritaglio possibile compatta per nascondere la visualizzazione anziché aumentare le dimensioni per mostrare la visualizzazione.

Per altre informazioni sugli effetti rivelare Android 5.0 in, vedere [usare l'effetto di rivelare](http://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Movimento curvo

Oltre a queste funzionalità di animazione, Android 5.0 fornisce inoltre nuove API che consentono di specificare le curve di tempo e il movimento delle animazioni. Android 5.0 Usa queste curve per eseguire l'interpolazione di spostamento temporale e quelli spaziale durante le animazioni. Tre curve sono definite in Android 5.0:

-   **Fast\_out\_lineare\_nelle** &ndash; accelera rapidamente e in continua crescita fino alla fine dell'animazione.

-   **Fast\_out\_lento\_nelle** &ndash; Accelerates rallenta rapidamente e lentamente verso la fine dell'animazione.

-   **Lineare\_out\_lento\_nelle** &ndash; inizia con una velocità di picco e lentamente rallenta alla fine dell'animazione.

È possibile usare il nuovo `PathInterpolator` classe per specificare come avviene l'interpolazione di animazione. `PathInterpolator` è un interpolatore che attraversa i percorsi animazione in base ai punti di controllo e curve di movimento. Per altre informazioni su come specificare le impostazioni di movimento curvo in Android 5.0, vedere [movimento curvo usare](http://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Visualizza ombreggiature & l'elevazione dei privilegi

In Android 5.0, è possibile specificare il *elevazione* di una vista impostando un nuovo `Z` proprietà. Una maggiore `Z` valore fa sì che la vista eseguire il cast di un'ombreggiatura più grande in background, rendendo la visualizzazione sembra float superiore rispetto allo sfondo. È possibile impostare l'elevazione iniziale di una vista tramite la configurazione relativa `elevation` attributo nel layout.

L'esempio seguente illustra le ombreggiature, eseguire il cast da un oggetto vuoto `TextView` controllare quando il relativo attributo di elevazione dei privilegi è impostata per 2dp 4dp e 6dp, rispettivamente:

[![Screenshot delle ombreggiature visualizzazione più grande progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Visualizzare le impostazioni di shadow possono essere statiche (come illustrato in precedenza) o possono essere usati nelle animazioni per rendere una vista ad aumentare temporaneamente di sopra di sfondo della visualizzazione. È possibile usare il `ViewPropertyAnimator` classe per animare l'elevazione di una vista. L'elevazione di una vista è la somma del layout `elevation` impostazione oltre a una `translationZ` proprietà che è possibile impostare tramite un `ViewPropertyAnimator` chiamata al metodo.

Per altre informazioni su shadows di visualizzazione in Android 5.0, vedere [Shadows definizione e le viste di ritaglio](http://developer.android.com/training/material/shadows-clipping.html).


## <a name="color-features"></a>Funzioni del colore

Android 5.0 fornisce due nuove funzionalità per la gestione di colore nelle App:

-   *Tinte drawable* consente di modificare i colori dell'asset immagini contenute modificando un attributo di layout.

-   *Estrazione di colore notificate all'utente* rende possibile per personalizzare dinamicamente il tema colori dell'app per il coordinamento con la tavolozza dei colori di un'immagine visualizzata.


### <a name="drawable-tinting"></a>Tinte drawable

Android 5.0 layout riconosce un nuovo `tint` attributo che è possibile usare per impostare il colore di drawable senza dover creare più versioni di queste risorse per visualizzare colori diversi. Per usare questa funzionalità, si definisce una mappa di bit come una maschera alfa e l'utilizzo di `tint` attributo per definire il colore dell'asset. Questo rende possibile per poter creare gli asset di una sola volta e li nel layout in modo che corrisponda al tema dei colori.

Nell'esempio seguente, un asset immagine singola &ndash; un logo bianco con uno sfondo trasparente &ndash; viene usato per creare varianti tinta:

![Logo di Xamarin vuoti con sfondo trasparente](lollipop-images/xamarin-logo-white.png)

Questo logo viene visualizzato di sopra di uno sfondo circolare blu come mostrato negli esempi seguenti. L'immagine a sinistra è come il logo viene visualizzato senza un `tint` impostazione. Nel centro, il logo dell'immagine `tint` attributo è impostato su un colore grigio scuro. Nell'immagine a destra, `tint` è impostata su un grigio chiaro:

![Esempi del logo precedente con impostazioni diverse tinta](lollipop-images/drawable-tinting.png)

Per altre informazioni sulla colorazione drawable in Android 5.0, vedere [tinte Drawable](http://developer.android.com/training/material/drawables.html#DrawableTint).


### <a name="prominent-color-extraction"></a>Estrazione di colore notificate all'utente

Il nuovo Android 5.0 `Palette` classe consente di estrarre i colori da un'immagine in modo che è possibile applicare in modo dinamico per una tavolozza dei colori personalizzati. Il `Palette` classe estrae i sei colori da un'immagine e i colori in base alla loro i livelli relativi di luminosità e saturazione colore delle etichette:

-   Pieno di vitalità

-   Scuro pieno di vitalità

-   Light pieno di vitalità

-   Disattivazione dell'audio

-   Disattivato scuro

-   Disattivato light

Nelle schermate seguenti, ad esempio, una foto Visualizza app estrae i colori notificate all'utente dall'immagine sullo schermo e Usa questi colori per adattare la combinazione di colori dell'app che l'immagine corrisponda:

[![Screenshot delle estrazioni di colore del tema rosa, verde e blu](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Negli screenshot precedente, la barra delle azioni è impostata su "light di pieno di vitalità" estratti colore e lo sfondo è impostato su "dark di pieno di vitalità" estratti colore. In ogni esempio precedente, una riga di quadrati di colore di piccole dimensioni è inclusa per illustrare i colori della tavolozza che sono stati estratti dall'immagine.

Per ulteriori informazioni sull'estrazione di colore in Android 5.0, vedere [estrazione notificate all'utente i colori da un'immagine](http://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Nuovi widget dell'interfaccia utente

Android 5.0 sono stati introdotti due nuovi widget dell'interfaccia utente:

-   `RecyclerView` &ndash; Un gruppo di visualizzazione che consente di visualizzare un elenco di elementi che è possibile scorrere.

-   `CardView` &ndash; Un layout di base con angoli arrotondati.

Entrambi i widget includono il supporto integrati per le funzionalità di tema Material; ad esempio, `RecyclerView` Usa animazioni per l'aggiunta e rimozione di viste, e `CardView` Usa Visualizza ombreggiature per visualizzare ogni scheda spostata sopra lo sfondo. Nelle schermate riportate di seguito sono riportati alcuni esempi di questi nuovi widget:

[![Alcune schermate dell'App compilate con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Nella schermata a sinistra è riportato un esempio di `RecyclerView` come in un'app di posta elettronica e la schermata su destra è un esempio di `CardView` come usarlo in un'app di prenotazione di viaggio.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` è simile a `ListView,` ma meglio è adatto per grandi set di viste o gli elenchi con gli elementi che cambiano in modo dinamico. Ad esempio `ListView,` è specificare un adattatore per accedere al set di dati sottostante. Tuttavia, a differenza `ListView,` usi un *gestore di layout* per posizionare gli elementi all'interno di `RecyclerView`. Il gestore di layout si occupa anche della visualizzazione riciclo; gestisce il riutilizzo delle viste di elemento che non sono più visibili all'utente.

Quando si usa un' `RecyclerView` widget, è necessario specificare un `LayoutManager` e un adapter. Come illustrato nella figura `LayoutManager` è l'intermediario tra l'adapter e il `RecyclerView`:

![Diagramma di RecyclerView con supporto LayoutManager, Adapter e set di dati](lollipop-images/recyclerview-diagram.png)

Gli screenshot seguenti illustrano un `RecyclerView` che contiene 100 elementi (ogni elemento è costituito da un `ImageView` e un `TextView`):

[![Screenshot di un'app di RecyclerView lo scorrimento di immagini](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` gestisce questo set di dati di grandi dimensioni con facilità &ndash; lo scorrimento dall'inizio dell'elenco alla fine dell'elenco in questo esempio di app richiede solo pochi secondi. `RecyclerView` supporta anche le animazioni; in effetti, per impostazione predefinita vengono abilitate animazioni per l'aggiunta e rimozione di elementi. Quando un elemento viene aggiunto a un `RecyclerView`, viene applicata la dissolvenza in come illustrato in questa sequenza di screenshot:

[![Screenshot di frame di dissolvenza della voce una foto in frame](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Per altre informazioni sulle `RecyclerView`, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>Widget CardView

`CardView` è una visualizzazione semplice che simuli una scheda a virgola mobile con angoli arrotondati. Poiché `CardView` ha ombre visualizzazione incorporata, fornisce un modo semplice per aggiungere profondità visual all'app. Gli screenshot seguenti mostrano tre esempi orientati al testo di `CardView`:

[![Screenshot di esempio di App con gli elementi basati su widget CardView RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Ognuna delle schede nell'esempio precedente contiene un `TextView`; il colore di sfondo viene impostato tramite la `cardBackgroundColor` attributo.

Per altre informazioni sulle `CardView`, vedere [widget CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notifiche migliorate

Il sistema di notifica in Android 5.0 è stato aggiornato in modo significativo con un nuovo formato visivo e nuove funzionalità. Le notifiche sono stati completamente rinnovati in Android 5.0. Ad esempio, le notifiche in Android 5.0 ora usano testo scuro su uno sfondo chiaro:

![Esempio di una notifica di Android 5.0 non espansa](lollipop-images/expanded-notification-contracted.png)

Quando viene visualizzata un'icona di grandi dimensioni in una notifica (come illustrato nell'esempio precedente), Android 5.0 presenta la piccola icona come notifica badge sull'icona di grandi dimensioni. 

In Android 5.0, le notifiche possono essere visualizzati anche nella schermata di blocco dispositivi.
Ad esempio, ecco uno screenshot di esempio di una schermata di blocco con una singola notifica:

[![Screenshot della notifica vengano visualizzati nella schermata di blocco](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Gli utenti possono toccare due volte una notifica nella schermata di blocco per sbloccare il dispositivo e passare all'app che ha avuto origine la notifica, oppure scorrere rapidamente per ignorarla. Le notifiche sono una nuova *visibilità* l'impostazione che determina la quantità di contenuto possono essere visualizzati nella schermata di blocco. Gli utenti possono scegliere se consentire contenuti sensibili da visualizzare nelle notifiche schermata di blocco.

Android 5.0 introduce un nuovo formato di presentazione di notifica ad alta priorità chiamato *Heads-up*. Le notifiche Heads-up scorrere verso il basso dalla parte superiore della schermata per pochi secondi e quindi riesame nuovamente per la sfumatura di notifica nella parte superiore della schermata. Le notifiche Heads-up rendono possibili per il sistema dell'interfaccia utente di inserire informazioni importanti davanti all'utente senza interrompere l'attività attualmente in esecuzione. Nell'esempio seguente viene illustrata una semplice notifica Heads-up che viene visualizzata sopra un'app:

[![Esempio di una notifica heads-up](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Le notifiche Heads-up in genere vengono usate per gli eventi seguenti:

-   Un nuovo messaggio successivo

-   Una chiamata in arrivo

-   Indicazione di batteria in esaurimento

-   Attivare un allarme

Android 5.0 consente di visualizzare una notifica nel formato Heads-up solo quando è un'impostazione di priorità alta o max.

In Android 5.0, è possibile fornire i metadati di notifica per Android ordinare e visualizzare le notifiche in modo più intelligente. Android 5.0 consente di organizzare le notifiche in base alla priorità, visibilità e la categoria.
Categorie di notifica vengono usate per filtrare le notifiche che possono essere presentate quando il dispositivo si trova in *non disturbare* modalità.

Per informazioni dettagliate sulla creazione e l'avvio di notifiche con le funzionalità più recenti di Android 5.0, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Nuove API

Oltre alle nuove funzionalità e aspetto descritta in precedenza, Android 5.0 aggiunge nuove API che estendono la funzionalità di contenuti multimediali esistenti, archiviazione e funzionalità wireless e di connettività. Inoltre, Android 5.0 include nuove API che forniscono supporto per una nuova funzionalità di utilità di pianificazione di processo.

### <a name="camera"></a>Fotocamera

Android 5.0 fornisce diverse nuove API per le funzionalità avanzate della fotocamera. Il nuovo `Android.Hardware.Camera2` dello spazio dei nomi include le funzionalità per accesso fotocamera singoli dispositivi connessi a un dispositivo Android. Inoltre, `Android.Hardware.Camera2` modella ogni dispositivo fotocamera come pipeline: accetta una richiesta di acquisizione, acquisisce l'immagine e quindi restituisce il risultato. Questo approccio rende possibile per le app accodare più richieste di acquisizione in un dispositivo di fotocamera.

Le API seguenti rendono possibile queste nuove funzionalità:

-   `CameraManager.GetCameraIdList` &ndash; Consente di accedere a livello di programmazione i dispositivi webcam; si utilizza `CameraManager.OpenCamera` per connettersi a un dispositivo specifico della fotocamera.

-   `CameraCaptureSession` &ndash; Consente di acquisire o crea un flusso di immagini dal dispositivo della fotocamera. Si implementa un `CameraCaptureSession.CaptureListener` interfaccia per gestire i nuovi eventi di acquisizione immagine.

-   `CaptureRequest` &ndash; Definisce i parametri di acquisizione.

-   `CaptureResult` &ndash; Fornisce i risultati di un'operazione di acquisizione immagine.

Per altre informazioni sulla nuova fotocamera API in Android 5.0, vedere [multimediali](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Riproduzione audio

Android 5.0 aggiornamenti il `AudioTrack` classe per una migliore riproduzione audio:

-   `ENCODING_PCM_FLOAT` &ndash; Configura `AudioTrack` per accettare dati audio in formato a virgola mobile per una migliore dell'intervallo dinamico, maggiore capacità aggiuntiva e qualità più elevata (grazie a una maggiore precisione). Inoltre, formato a virgola mobile consente di evitare il ritaglio di audio.

-   `ByteBuffer` &ndash; È ora possibile fornire dati audio da `AudioTrack` come matrice di byte.

-   `WRITE_NON_BLOCKING` &ndash; Questa opzione semplifica la memorizzazione nel buffer e il multithreading per alcune app.

Per altre informazioni sulle `AudioTrack` miglioramenti in Android 5.0, vedere [multimediali](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controllo della riproduzione multimediale

Android 5.0 introduce le nuove `Android.Media.MediaController` classe, che sostituisce `RemoteControlClient`. `Android.Media.MediaController` fornisce un controllo API semplificata di trasporto e offre il controllo del thread-safe di riproduzione all'esterno del contesto dell'interfaccia utente. Le seguenti nuove API di gestire il controllo di trasporto:

-   `Android.Media.Session.MediaSession` &ndash; Una sessione di controllo media che gestisce più controller. Si chiama `MediaSession.GetSessionToken` per richiedere un token che l'app Usa per interagire con la sessione.

-   `MediaController.TransportControls` &ndash; I comandi, ad esempio di trasporto gestisce **riprodurre**, **arrestare**, e **Skip**.

Inoltre, è possibile usare il nuovo `Android.App.Notification.MediaStyle` classe per associare una sessione di supporto con il contenuto di notifica avanzata (ad esempio l'estrazione e visualizzazione di un album).

Per altre informazioni sulle nuove funzionalità di controllo della riproduzione multimediale in Android 5.0, vedere [multimediali](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Archiviazione

Android 5.0 aggiorna il Framework di accesso di archiviazione per rendere più semplice per lavorare con documenti e le directory delle applicazioni:

-   Per selezionare una sottostruttura di directory, è possibile compilare e inviare un `Android.Intent.Action.OPEN_DOCUMENT_TREE` finalità. Questa finalità, il sistema visualizzare tutte le istanze del provider che supportano la selezione di sottoalbero. quindi, l'utente Naviga e consente di selezionare una directory.

-   Per creare e gestire nuovi documenti o le directory in un punto qualsiasi in un sottoalbero, si utilizza la nuova `CreateDocument`, `RenameDocument`, e `DeleteDocument` metodi di `DocumentsContract`.

-   Per ottenere i percorsi a directory supporti su tutti i dispositivi di archiviazione condivisa, si chiama il nuovo `Android.Content.Context.GetExternalMediaDirs` (metodo).

Per altre informazioni sulle nuove risorse di archiviazione API in Android 5.0, vedere [archiviazione](http://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Connettività e wireless

Android 5.0 aggiunge i seguenti miglioramenti di API per la connettività e wireless:

-   Nuove *più reti* API che rendono possibili per le app trovare e selezionare le reti con funzionalità specifiche prima di effettuare una connessione.

-   Funzionalità di trasmissione Bluetooth che consente a un dispositivo Android 5.0 e fungono da una periferica Bluetooth low energy.

-   Miglioramenti per NFC che rendono più semplice l'utilizzo delle funzionalità near field communications per la condivisione dei dati con altri dispositivi.

Per altre informazioni sulle nuove wireless e connettività API in Android 5.0, vedere [Wireless e connettività](http://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Pianificazione dei processi

Android 5.0 introduce un nuovo `JobScheduler` API che consente agli utenti di ridurre al minimo della batteria, la pianificazione di determinate attività venga eseguita solo quando il dispositivo sia connesso e l'addebito. Questa funzionalità di utilità di pianificazione di processo è anche utilizzabile per la pianificazione di un'attività da eseguire quando le condizioni sono più adatti a tale attività, ad esempio quando il dispositivo è connesso tramite una rete Wi-Fi anziché una rete a consumo per scaricare un file di grandi dimensioni.

Per altre informazioni sul nuovo processo di pianificazione di API in Android 5.0, vedere [pianificazione di processi](http://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Riepilogo

Questo articolo è fornita una panoramica delle nuove funzionalità importanti nella versione 5.0 di Android per gli sviluppatori di app xamarin. Android:

-   Tema Material

-   Animations

-   Visualizza ombreggiature e l'elevazione dei privilegi

-   Le funzionalità di colore, ad esempio tinte drawable e notificate all'utente di colore estrazione

-   Il nuovo `RecyclerView` e `CardView` widget

-   Miglioramenti di notifica

-   Nuove API per la fotocamera, la riproduzione audio, controllo media, archiviazione, connettività wireless/e pianificazione dei processi

Se si ha familiarità con lo sviluppo di Xamarin. Android, leggere [installazione e configurazione](~/android/get-started/installation/index.md) che consentono di introduzione a xamarin. Android.
[Hello, Android](~/android/get-started/hello-android/index.md) è un'eccellente introduzione per imparare a creare i progetti Android.



## <a name="related-links"></a>Collegamenti correlati

- [L Android Developer Preview](http://developer.android.com/preview/index.html)
- [Ottenere Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Material Design](http://developer.android.com/preview/material/index.html)
- [Principi di progettazione dei materiali](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
