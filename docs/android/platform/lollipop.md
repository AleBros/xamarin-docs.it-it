---
title: Caratteristiche di Lollipop
description: Questo articolo fornisce una panoramica di alto livello delle nuove funzionalità introdotte in Android 5.0 (Lollipop). Queste funzionalità includono un nuovo stile dell'interfaccia utente denominato Tema materiale, nonché nuove funzionalità di supporto come animazioni, ombreggiature di visualizzazione e tinte disegnabili. Android 5.0 include anche notifiche avanzate, due nuovi widget dell'interfaccia utente, una nuova utilità di pianificazione dei processi e una manciata di nuove API per migliorare l'archiviazione, la rete, la connettività e le funzionalità multimediali.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292148"
---
# <a name="lollipop-features"></a>Caratteristiche di Lollipop

_Questo articolo fornisce una panoramica di alto livello delle nuove funzionalità introdotte in Android 5.0 (Lollipop). Queste funzionalità includono un nuovo stile dell'interfaccia utente denominato Tema materiale, nonché nuove funzionalità di supporto come animazioni, ombreggiature di visualizzazione e tinte disegnabili. Android 5.0 include anche notifiche avanzate, due nuovi widget dell'interfaccia utente, una nuova utilità di pianificazione dei processi e una manciata di nuove API per migliorare l'archiviazione, la rete, la connettività e le funzionalità multimediali._

## <a name="lollipop-overview"></a>Lollipop Panoramica

Android 5.0 (Lollipop) introduce un nuovo linguaggio di progettazione, *Material Design*, e con esso un cast di supporto di nuove funzionalità per rendere le applicazioni più facili e intuitive da usare. Con Material Design, Android 5.0 non solo dà telefoni Android un lifting; fornisce anche una nuova serie di regole di progettazione per tablet basati su Android, computer desktop, orologi e smart TV. Queste regole di progettazione enfatizzano la semplicità e il minimalismo, facendo uso di attributi tattili familiari (come segnali realistici di superficie e bordo) per aiutare gli utenti a comprendere in modo rapido e intuitivo l'interfaccia.

Tema materiale è l'incarnazione di questi principi di progettazione dell'interfaccia utente in *Android.Material Theme* is the emoodiment of these UI design principles in Android. Questo articolo inizia coprendo le funzioni di supporto di Material Theme:

- **Animazioni** &ndash; *Animazioni di feedback* di tocco, animazioni di transizione di *attività,* animazioni di transizione dello stato di *visualizzazione* e un effetto *di rivelazione.*

- Le ombre della vista e `elevation` &ndash; le viste di **prospetto** ora hanno una proprietà;   viste con `elevation` valori più alti proiettano ombre più grandi sullo sfondo.

- **Caratteristiche a** &ndash; colori *La tinta disegnabile* consente di riutilizzare le risorse immagine modificandone il colore e l'estrazione *di colori prominente* consente di definire dinamicamente il tema dell'app in base ai colori di un'immagine.

Molte funzionalità del tema materiale sono già incorporate nell'esperienza dell'interfaccia utente di Android 5.0, mentre altre devono essere aggiunte in modo esplicito alle app. Ad esempio, alcune visualizzazioni standard (ad esempio i pulsanti) includono già animazioni di feedback tocco, mentre le app devono abilitare la maggior parte delle ombreggiature di visualizzazione.

Oltre ai miglioramenti dell'interfaccia utente causati tramite Material Theme, Android 5.0 include anche diverse altre nuove funzionalità trattate in questo articolo:In addition to the UI improvements through Material Theme, Android 5.0 includes also several other new features that are covered in this article:

- **Notifiche migliorate** &ndash; Le notifiche in Android 5.0 sono state significativamente aggiornate con un nuovo aspetto, il supporto per le notifiche lockscreen e un nuovo formato di presentazione per le notifiche *Heads-up.*

- **Nuovi widget dell'interfaccia utente** &ndash; Il nuovo `RecyclerView` widget rende più semplice per `CardView` le app trasmettere set di dati di grandi dimensioni e informazioni complesse e il nuovo widget fornisce un formato di presentazione semplificato per la visualizzazione di testo e immagini.

- **Le nuove API** &ndash; Android 5.0 aggiungono nuove API per il supporto di rete multipla, una migliore connettività Bluetooth, una gestione più semplice dello storage e un controllo più flessibile dei lettori multimediali multimediali e dei dispositivi della fotocamera. Una nuova funzionalità di pianificazione dei processi è disponibile per eseguire le attività in modo asincrono a orari pianificati. Questa funzione consente di migliorare la durata della batteria, ad esempio pianificando le attività da eseguire quando il dispositivo è collegato e in carica.

## <a name="requirements"></a>Requisiti

Per utilizzare le nuove funzionalità di Android 5.0 nelle app basate su Xamarin, è necessario quanto segue:

- **Xamarin.Android** &ndash; Xamarin.Android 4.20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- **Android SDK** &ndash; Android 5.0 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

- **Java Developer Kit** &ndash; Xamarin.Android richiede [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sta sviluppando per il livello API 24 o superiore (JDK 1.8 supporta anche i livelli API precedenti a24, incluso Lollipop). La versione a 64 bit di JDK 1.8 è necessaria se si utilizzano controlli personalizzati o Forms Previewer.

È possibile continuare a utilizzare [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si sviluppa specificamente per il livello API 23 o versioni precedenti.

## <a name="setting-up-an-android-50-project"></a>Configurazione di un progetto Android 5.0

Per creare un progetto Android 5.0, è necessario installare gli strumenti e i pacchetti SDK più recenti. Utilizzare la procedura seguente per impostare un progetto Xamarin.Android destinato a Android 5.0:Use the following steps to set up a Xamarin.Android project that targets Android 5.0:

1. Installa gli strumenti Xamarin.Android e attiva la tua licenza Xamarin. Vedere Installazione e installazione per ulteriori informazioni sull'installazione di Xamarin.Android.See [Setup and Installation](~/android/get-started/installation/index.md) for more information about installing Xamarin.Android.

2. Se si usa Visual Studio per Mac, installare gli aggiornamenti più recenti di Android 5.0.

3. Avviare Android SDK Manager (in Visual Studio per Mac, usare **Strumenti &gt; Apri Android SDK Manager)&hellip;** e installare Android SDK Tools 23.0.5 o versione successiva:

    [![Selezione degli strumenti di Android SDK in Android SDK Manager](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Inoltre, installare i pacchetti SDK di Android 5.0 più recenti (API 21 o versioni successive):

    [![Installazione di pacchetti SDK di Android 5.0 in Android SDK Manager](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Per ulteriori informazioni sull'utilizzo di Android SDK Manager, vedere [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Creare un nuovo progetto Xamarin.Android.Create a new Xamarin.Android project. Se non si ha familiarità con lo sviluppo Android con Xamarin, vedere Hello, Android per informazioni sulla creazione di progetti Android.If you are new to Android development with Xamarin, see [Hello, Android](~/android/get-started/hello-android/index.md) to learn about creating Android projects. Quando crei un progetto Android, assicurati di configurare le impostazioni della versione per Android 5.0.
   In Visual Studio per Mac passare a Opzioni di progetto Compilazione generale e impostare Framework di destinazione su Android 5.0 (Lollipop) o versione successiva:In Visual Studio for Mac, navigate to **Project &gt; Options Build &gt; General** and set Target **framework** to Android **5.0 (Lollipop)** or later:

    ![Impostazione di Target Framwework su Android 5.0 Lollipop](lollipop-images/target-framework.png)

   In **Opzioni &gt; &gt; di progetto Compila applicazione Android**, impostare la versione minima e la versione di Android di destinazione su Automatico - utilizzare la versione del framework di **destinazione**:

    ![Impostazione delle versioni minima e di destinazione di Android su Automatico](lollipop-images/minimum-android-version.png)

5. Configurare un emulatore o un dispositivo Android per testare l'app. Se si usa un emulatore, vedere [Configurazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md) per informazioni su come configurare un emulatore Android da usare con Xamarin Studio o Visual Studio.If you are using an emulator, see Android Emulator Setup to learn how to configure an Android emulator for use with Xamarin Studio or Visual Studio. Se usi un dispositivo Android, vedi [Configurazione dell'SDK](https://developer.android.com/preview/setup-sdk.html) di anteprima per informazioni su come aggiornare il dispositivo per Android 5.0. Per configurare il dispositivo Android per l'esecuzione e il debug di applicazioni Xamarin.Android, vedere [Configurare](~/android/get-started/installation/set-up-device-for-development.md)il dispositivo per lo sviluppo .

Nota: se si sta aggiornando un progetto Android esistente destinato all'anteprima di Android L, è necessario aggiornare la versione framework di **destinazione** e **Android** ai valori descritti in precedenza.

## <a name="important-changes"></a>Modifiche importanti

Le app Android pubblicate in precedenza potrebbero essere influenzate dalle modifiche in Android 5.0. In particolare, Android 5.0 utilizza un nuovo runtime e un formato di notifica modificato in modo significativo.

### <a name="android-runtime"></a>Android Runtime

Android 5.0 utilizza il nuovo Android Runtime (ART) come runtime predefinito anziché Dalvik. ART implementa diverse nuove funzionalità principali:

- La &ndash; **compilazione anticipata (AOT)** può migliorare le prestazioni dell'app compilando il codice dell'app prima che l'app venga avviata per la prima volta. Quando viene installata un'app, ART genera un eseguibile dell'app compilato per il dispositivo di destinazione.

- **I miglioramenti migliorati di Garbage Collection (GC)** &ndash; GC in ART possono anche migliorare le prestazioni dell'app. La Garbage Collection ora usa una pausa GC anziché due e le operazioni GC simultanee vengono completate in modo più tempestivo.

- **Il debug** &ndash; delle app ART migliorato fornisce maggiori dettagli diagnostici per facilitare l'analisi delle eccezioni e dei rapporti sugli arresti anomali.

Le app esistenti dovrebbero &ndash; funzionare senza modifiche in ART, ad eccezione delle app che sfruttano tecniche specifiche del runtime Dalvik precedente, che potrebbero non funzionare in ART. Per ulteriori informazioni su queste modifiche, vedere [Verifica del comportamento dell'app in Android Runtime (ART)](https://developer.android.com/guide/practices/verifying-apps-art.html).

### <a name="notification-changes"></a>Modifiche alle notifiche

Le notifiche sono cambiate in modo significativo in Android 5.0:

- **Suoni e vibrazioni sono gestiti** &ndash; in modo diverso `Notification.Builder` I `Ringtone`suoni `MediaPlayer`di `Vibrator`notifica e le vibrazioni sono ora gestiti da invece di , , e .

- **Nuova combinazione** &ndash; di colori In conformità con il tema materiale, le notifiche vengono visualizzate con testo scuro su sfondi bianchi o molto chiari. Inoltre, i canali alfa nelle icone di notifica possono essere modificati da Android per coordinarsi con le combinazioni di colori di sistema.

- **Notifiche** &ndash; di blocco schermo Le notifiche possono ora essere visualizzate nella schermata di blocco del dispositivo.

- Le notifiche **Heads-up** &ndash; ad alta priorità ora vengono visualizzate in una piccola finestra mobile (notifica Heads-up) quando il dispositivo è sbloccato e lo schermo è acceso.

Nella maggior parte dei casi, il porting della funzionalità di notifica dell'app esistente in Android 5.0 richiede i passaggi seguenti:In most cases, porting existing app notification functionality to Android 5.0 requires the following steps:

1. Convertire il codice `Notification.Builder` da `NotificationsCompat.Builder`utilizzare (o ) per la creazione di notifiche.

2. Verificare che le risorse di notifica esistenti siano visualizzabili nella nuova combinazione di colori Tema materiale.

3. Decidi la visibilità che le tue notifiche devono avere quando vengono presentate sulla schermata di blocco. Se una notifica non è pubblica, quali contenuti devono essere visualizzati nella schermata di blocco?

4. Impostare la categoria delle notifiche in modo che vengano gestite correttamente nella nuova modalità *Non disturbare.*

Se le notifiche presentano controlli di trasporto, visualizzare lo stato di riproduzione multimediale, utilizzare `RemoteControlClient`o chiamare `ActivityManager.GetRecentTasks`, vedere Modifiche importanti del [comportamento](https://developer.android.com/preview/api-overview.html#Behaviors) per ulteriori informazioni sull'aggiornamento delle notifiche per Android 5.0.

Per informazioni sulla creazione di notifiche in Android, vedere [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Tema Material

Il nuovo tema materiale Android 5.0 porta ampie modifiche all'aspetto dell'interfaccia utente di Android. Gli elementi visivi ora utilizzano superfici tattili che assumono la grafica audace, la tipografia e i colori vivaci del design basato sulla stampa. Esempi di tema materiale sono illustrati nelle schermate seguenti:Examples of Material Theme are depicted in the following screenshots:

[![Screenshot della schermata iniziale del tema del materiale, della schermata delle app e della schermata di impostazione](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 ti saluta con la schermata iniziale mostrata a sinistra. Lo screenshot centrale è la prima schermata dell'elenco delle app e la schermata a destra è la schermata **Impostazioni.** La specifica [Material Design](https://material.io/guidelines/material-design/introduction.html) di Google spiega le regole di progettazione alla base del nuovo concetto di tema dei materiali.

Tema materiale include tre sapori predefiniti che puoi usare nella `Theme.Material` tua app: il `Theme.Material.Light` tema scuro (impostazione predefinita), il tema e il `Theme.Material.Light.DarkActionBar` tema:

[![Screenshot dei temi Scuro, Chiaro e DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Per ulteriori informazioni sull'utilizzo delle funzioni del tema materiale nelle app Xamarin.Android, vedere [Tema materiale](~/android/user-interface/material-theme.md).

## <a name="animations"></a>Animations

Android 5.0 offre animazioni di feedback tocco, animazioni di transizione di attività e animazioni di transizione dello stato di visualizzazione per rendere le interfacce dell'app più intuitive da usare. Inoltre, le app Android 5.0 possono usare le animazioni degli effetti di *rivelazione* per nascondere o rivelare le visualizzazioni. È possibile utilizzare le impostazioni di *movimento curvo* per configurare la velocità o la lentezza di rendering delle animazioni.

### <a name="touch-feedback-animations"></a>Animazioni di feedback tocco

Le animazioni di feedback tramite tocco forniscono agli utenti un feedback visivo quando viene toccata una visualizzazione. Ad esempio, i pulsanti ora visualizzano un effetto a catena quando vengono toccati questa è l'animazione di feedback tocco predefinito in Android 5.0.For example, buttons now display a ripple effect when they are touched &ndash; this is the default touch feedback animation in Android 5.0. L'animazione Ripple `RippleDrawable` viene implementata dalla nuova classe. L'effetto increspatura può essere configurato per terminare ai limiti della vista o estendersi oltre i limiti della visualizzazione. Ad esempio, la sequenza di schermate seguente illustra l'effetto a catena in un pulsante durante l'animazione tramite tocco:For example, the following sequence of screenshots illustrates the ripple effect in a button during touch animation:

![Screenshot fotogramma per fotogramma dell'animazione increspatura su un pulsante](lollipop-images/touch-animation.png)

Il contatto tocco iniziale con il pulsante si verifica nella prima immagine a sinistra, mentre la sequenza rimanente (da sinistra a destra) illustra come l'effetto increspatura si estende fino al bordo del pulsante. Al termine dell'animazione a catena, la visualizzazione torna all'aspetto originale. L'animazione a increspatura predefinita viene eseguita in una frazione di secondo, ma la lunghezza dell'animazione può essere personalizzata per periodi di tempo più lunghi o più brevi.

Per altre informazioni sulle animazioni di feedback tocco in Android 5.0, vedere [Personalizzare il feedback](https://developer.android.com/training/material/animations.html#Touch)tocco.

### <a name="activity-transition-animations"></a>Animazioni di transizione di attivitàActivity Transition Animations

Le animazioni di transizione di attività offrono agli utenti un senso di continuità visiva quando un'attività passa a un'altra. Le app possono specificare tre tipi di animazioni di transizione:

- **Immettere transizione** &ndash; per quando un'attività entra nella scena.

- **Transizione** &ndash; di uscita per quando un'attività esce dalla scena.

- **Transizione** &ndash; di elementi condivisi Per quando una visualizzazione comune a due attività cambia man mano che la prima attività passa alla successiva.

Ad esempio, la sequenza di schermate seguente illustra una transizione di elemento condiviso:For example, the following sequence of screenshots illustrates a shared element transition:

[![Screenshot fotogramma per fotogramma di un'animazione di transizione di elementi condivisi](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento condiviso (una foto di un bruco) è una delle diverse viste della prima attività; si ingrandisce fino a diventare l'unica vista nella seconda attività quando la prima attività passa alla seconda.

#### <a name="enter-transition-animation-types"></a>Immissione dei tipi di animazione di transizione

Per immettere le transizioni, Android 5.0 fornisce tre tipi di animazioni:For enter transitions, Android 5.0 provides three types of animations:

- **Animazione** &ndash; esplosione Ingrandisce una vista dal centro della scena.

- **Animazione** &ndash; diapositiva Sposta una vista da uno dei bordi di una scena.

- **Animazione** &ndash; dissolvenza Dissolvenza di una vista nella scena.

#### <a name="exit-transition-animation-types"></a>Uscire dai tipi di animazione di transizione

Per le transizioni di uscita, Android 5.0 fornisce tre tipi di animazioni:For exit transitions, Android 5.0 provides three types of animations:

- &ndash; **Animazione Esplodi** Riduce una vista al centro della scena.

- **Animazione** &ndash; diapositiva Sposta una vista verso uno dei bordi di una scena.

- **Animazione** &ndash; dissolvenza Dissolvenza di una vista fuori scena.

#### <a name="shared-element-transition-animation-types"></a>Tipi di animazione di transizione di elementi condivisiShared Element Transition Animation Types

Le transizioni di elementi condivisi supportano più tipi di animazioni, ad esempio:Shared element transitions support multiple types of animations, such as:

- Modifica del layout o dei limiti di ritaglio di una vista.

- Modifica della scala e della rotazione di una vista.

- Modifica delle dimensioni e del tipo di scala per una vista.

Per altre informazioni sulle animazioni di transizione di attività in Android 5.0, vedere [Personalizzare le transizioni](https://developer.android.com/training/material/animations.html#Transitions)di attività.

### <a name="view-state-transition-animations"></a>Animazioni di transizione dello stato di visualizzazioneView State Transition Animations

Android 5.0 consente l'esecuzione delle animazioni quando cambia lo stato di una visualizzazione. È possibile animare le transizioni dello stato di visualizzazione utilizzando una delle tecniche seguenti:You can animate view state transitions by using one of the following techniques:

- Creare oggetti drawable che animano le modifiche di stato associate a una determinata visualizzazione. La `AnimatedStateListDrawable` nuova classe consente di creare oggetti drawable che visualizzano animazioni tra le modifiche dello stato di visualizzazione.

- Definire la funzionalità di animazione che viene eseguita quando lo stato di una visualizzazione cambia. La `StateListAnimator` nuova classe consente di definire un animatore che viene eseguito quando lo stato di una vista cambia.

Per ulteriori informazioni sulle animazioni di transizione dello stato di visualizzazione in Android 5.0, consultate [Animare le modifiche dello stato](https://developer.android.com/training/material/animations.html#ViewState)di visualizzazione.

### <a name="reveal-effect"></a>Effetto Rivela

*L'effetto di rivelazione* è un cerchio di ritaglio che cambia raggio per rivelare o nascondere una vista. È possibile controllare questo effetto impostando il raggio iniziale e finale del cerchio di ritaglio. La seguente sequenza di schermate illustra un'animazione dell'effetto di rivelazione dal centro dello schermo:

[![Screenshot fotogramma per fotogramma di un'animazione di rivelazione](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La sequenza successiva illustra un'animazione dell'effetto di rivelazione che viene eseguita dall'angolo inferiore sinistro dello schermo:

[![Screenshot fotogramma per fotogramma dell'animazione di ritaglio](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Le animazioni Reveal possono essere invertite; ovvero il cerchio di ritaglio può ridursi per nascondere la vista anziché ingrandirla per visualizzare la vista.

Per ulteriori informazioni sull'effetto di rivelazione Android 5.0, consultate [Usare l'effetto Reveal](https://developer.android.com/training/material/animations.html#Reveal).

### <a name="curved-motion"></a>Movimento curvo

Oltre a queste funzionalità di animazione, Android 5.0 fornisce anche nuove API che consentono di specificare le curve di tempo e movimento delle animazioni. Android 5.0 usa queste curve per interpolare il movimento temporale e spaziale durante le animazioni. In Android 5.0 sono definite tre curve:

- **Veloce\_\_mente\_lineare in** &ndash; Accelera rapidamente e continua ad accelerare fino alla fine dell'animazione.

- **\_Velocemente\_\_lento in** &ndash; Accelera rapidamente e lentamente decelera verso la fine dell'animazione.

- **\_Lineare\_\_fuori lento in** &ndash; Inizia con una velocità di picco e decelera lentamente fino alla fine dell'animazione.

È possibile utilizzare `PathInterpolator` la nuova classe per specificare come avviene l'interpolazione di movimento. `PathInterpolator`è un interpolatore che attraversa i tracciati di animazione in base ai punti di controllo e alle curve di movimento specificati. Per ulteriori informazioni su come specificare le impostazioni di movimento curvo in Android 5.0, consultate [Usare il movimento curvo.](https://developer.android.com/training/material/animations.html#CurvedMotion)

## <a name="view-shadows--elevation"></a>Vista delle ombreggiature &

In Android 5.0, è possibile specificare la quota `Z` *altimetrica* di una vista impostando una nuova proprietà. Un `Z` valore maggiore fa sì che la vista proietta un'ombra più grande sullo sfondo, facendo apparire la vista in alto sopra lo sfondo. È possibile impostare la quota altimetrica `elevation` iniziale di una vista configurandone l'attributo nel layout.

L'esempio seguente illustra le ombre `TextView` proiettate da un controllo vuoto quando il relativo attributo di elevazione è impostato rispettivamente su 2dp, 4dp e 6dp:

[![Screenshot di ombre di visualizzazione progessivamente più grandi](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Le impostazioni dell'ombra della vista possono essere statiche (come mostrato sopra) o possono essere utilizzate nelle animazioni per far sembrare che una vista si elevi temporaneamente sopra lo sfondo della vista. È possibile `ViewPropertyAnimator` utilizzare la classe per animare la quota altimetrica di una vista. L'elevazione di una vista è `elevation` la `translationZ` somma dell'impostazione `ViewPropertyAnimator` del layout più una proprietà che è possibile impostare tramite una chiamata al metodo.

Per ulteriori informazioni sulle ombreggiature delle viste in Android 5.0, consultate [Definizione di ombre e viste di ritaglio.](https://developer.android.com/training/material/shadows-clipping.html)

## <a name="color-features"></a>Caratteristiche colore

Android 5.0 offre due nuove funzionalità per la gestione del colore nelle app:

- *La tinta disegnabile* consente di modificare i colori delle risorse immagine modificando un attributo di layout.

- *L'estrazione dei* colori di rilievo consente di personalizzare dinamicamente il tema di colore dell'app per coordinarsi con la tavolozza dei colori di un'immagine visualizzata.

### <a name="drawable-tinting"></a>Tinta disegnabile

I layout di Android 5.0 riconoscono un nuovo `tint` attributo che è possibile utilizzare per impostare il colore dei drawables senza dover creare più versioni di queste risorse per visualizzare colori diversi. Per utilizzare questa funzione, definite una bitmap come `tint` maschera alfa e utilizzate l'attributo per definire il colore della risorsa. In questo modo è possibile creare le risorse una sola volta e colorarle nel layout in modo che corrispondano al tema.

Nell'esempio seguente, una &ndash; singola risorsa immagine un &ndash; logo bianco con uno sfondo trasparente viene utilizzata per creare varianti di tinta:

![Logo Xamarin bianco con sfondo trasparente](lollipop-images/xamarin-logo-white.png)

Questo logo viene visualizzato sopra uno sfondo circolare blu, come illustrato negli esempi seguenti. L'immagine a sinistra è come `tint` il logo appare senza un'impostazione. Nell'immagine centrale, l'attributo del `tint` logo è impostato su un grigio scuro. Nell'immagine a destra, `tint` è impostato su un grigio chiaro:

![Esempi del logo di cui sopra con diverse impostazioni di tinta](lollipop-images/drawable-tinting.png)

Per ulteriori informazioni sulla tinta disegnabile in Android 5.0, vedere [Tinta disegnabile](https://developer.android.com/training/material/drawables.html#DrawableTint).

### <a name="prominent-color-extraction"></a>Estrazione di colori prominenti

La nuova classe `Palette` Android 5.0 consente di estrarre i colori da un'immagine in modo da poterli applicare dinamicamente a una tavolozza di colori personalizzata. La `Palette` classe estrae sei colori da un'immagine ed etichetta questi colori in base ai livelli relativi di saturazione e luminosità del colore:

- Vibrante

- Vivace scuro

- Luce vibrante

- Disattivato

- Scuro silenziato

- Luce silenziata

Ad esempio, nelle schermate seguenti, un'app di visualizzazione delle foto estrae i colori prominenti dall'immagine visualizzata e usa questi colori per adattare la combinazione di colori dell'app in modo che corrisponda all'immagine:

[![Screenshot delle estrazioni dei colori verde, rosa e blu](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Nelle schermate precedenti, la barra delle azioni è impostata sul colore "vibrante" estratto e lo sfondo è impostato sul colore "vibrante scuro" estratto. In ogni esempio precedente, viene inclusa una riga di piccoli quadrati di colore per illustrare i colori della tavolozza estratti dall'immagine.

Per ulteriori informazioni sull'estrazione dei colori in Android 5.0, vedere [Estrazione di colori prominenti da un'immagine](https://developer.android.com/training/material/drawables.html#ColorExtract).

## <a name="new-ui-widgets"></a>Nuovi widget dell'interfaccia utente

Android 5.0 introduce due nuovi widget dell'interfaccia utente:

- `RecyclerView`&ndash; Gruppo di visualizzazioni che visualizza un elenco di elementi scorrevoli.

- `CardView`&ndash; Un layout di base con angoli arrotondati.

Entrambi i widget includono il supporto incorporato per le funzioni del tema materiale; ad esempio, `RecyclerView` utilizza le animazioni per `CardView` l'aggiunta e la rimozione di viste e le ombre della vista per far apparire ogni scheda fluttuante sopra lo sfondo. Esempi di questi nuovi widget sono mostrati nelle seguenti schermate:

[![Screenshot delle app create con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Lo screenshot a sinistra è `RecyclerView` un esempio di come usato in un'app `CardView` di posta elettronica, e la schermata a destra è un esempio di come usato in un'app di prenotazione viaggi.

### <a name="recyclerview"></a>RecyclerView

`RecyclerView`è simile, `ListView,` ma è più adatto per grandi set di viste o elenchi con elementi che cambiano dinamicamente. Come `ListView,` specificare un adattatore per accedere al set di dati sottostante. Tuttavia, `ListView,` a differenza di utilizzare `RecyclerView`un *gestore di layout* per posizionare gli elementi all'interno di . Il gestore di layout si occupa anche del riciclo della vista; gestisce il riutilizzo delle visualizzazioni elemento che non sono più visibili all'utente.

Quando si `RecyclerView` utilizza un widget, `LayoutManager` è necessario specificare un adattatore e un adattatore. Come mostrato in `LayoutManager` questa figura, è l'intermediario tra l'adattatore e il `RecyclerView`:

![Diagramma di RecyclerView con supporto di LayoutManager, Adattatore e Dataset](lollipop-images/recyclerview-diagram.png)

Le schermate seguenti `RecyclerView` illustrano un che contiene 100 elementi (ogni elemento è costituito da un `ImageView` e un): `TextView`

[![Screenshot di un'app RecyclerView che scorre le immagini](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`gestisce questo set &ndash; di dati di grandi dimensioni con facilità di scorrimento dall'inizio dell'elenco alla fine dell'elenco in questa applicazione di esempio richiede solo pochi secondi. `RecyclerView`supporta anche le animazioni; infatti, le animazioni per l'aggiunta e la rimozione di elementi sono abilitate per impostazione predefinita. Quando un elemento viene `RecyclerView`aggiunto a un oggetto , si dissolve come mostrato in questa sequenza di schermate:

[![Screenshot fotogramma per fotogramma di un elemento foto che si dissolve](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Per ulteriori `RecyclerView`informazioni, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

### <a name="cardview"></a>CardView

`CardView`è una vista semplice che simula una carta galleggiante con angoli arrotondati. Poiché `CardView` include ombreggiature di visualizzazione incorporate, offre un modo semplice per aggiungere profondità visiva all'app. Le schermate seguenti mostrano tre `CardView`esempi orientati al testo di:

[![Screenshot di esempio di app che usano RecyclerView con elementi basati su CardViewExample screenshots of apps using RecyclerView with CardView-based items](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Ognuna delle carte nell'esempio `TextView`precedente contiene un ; il colore di sfondo `cardBackgroundColor` viene impostato tramite l'attributo.

Per ulteriori `CardView`informazioni, vedere [CardView](~/android/user-interface/controls/card-view.md).

## <a name="enhanced-notifications"></a>Notifiche migliorate

Il sistema di notifica in Android 5.0 è stato significativamente aggiornato con un nuovo formato visivo e nuove funzionalità. Le notifiche hanno un nuovo look in Android 5.0. Ad esempio, le notifiche in Android 5.0 ora usano testo scuro su uno sfondo chiaro:For example, notifications in Android 5.0 now use dark text over a light background:

![Esempio di notifica Android 5.0 non espansa](lollipop-images/expanded-notification-contracted.png)

Quando viene visualizzata un'icona di grandi dimensioni in una notifica (come illustrato nell'esempio precedente), Android 5.0 presenta la piccola icona come un badge sull'icona grande.

In Android 5.0, le notifiche possono essere visualizzate anche nella schermata di blocco del dispositivo.
Ad esempio, ecco uno screenshot di esempio di una schermata di blocco con una singola notifica:For example, here is an example screenshot of a lockscreen with a single notification:

[![Screenshot della notifica visualizzata nella schermata di blocco](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Gli utenti possono effettuare un doppio tocco su una notifica nella schermata di blocco per sbloccare il dispositivo e passare all'app che ha originato la notifica oppure scorrere per ignorarla. Le notifiche hanno una nuova impostazione di *visibilità* che determina la quantità di contenuto che può essere visualizzata nella schermata di blocco. Gli utenti possono scegliere se consentire la visualizzazione di contenuti sensibili nelle notifiche della schermata di blocco.

Android 5.0 introduce un nuovo formato di presentazione di notifica ad alta priorità chiamato *Heads-up*. Le notifiche Heads-up scorrono verso il basso dalla parte superiore dello schermo per alcuni secondi e poi si ritirano all'ombra di notifica nella parte superiore dello schermo. Le notifiche Heads-up consentono all'interfaccia utente del sistema di inserire informazioni importanti davanti all'utente senza interrompere l'attività attualmente in esecuzione.
L'esempio seguente illustra una semplice notifica Heads-up che viene visualizzata sopra un'app:

[![Esempio di notifica heads-up](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Le notifiche Heads-up vengono in genere utilizzate per i seguenti eventi:

- Un nuovo messaggio successivo

- Una telefonata in arrivo

- Indicazione batteria scarica

- Un allarme

Android 5.0 visualizza una notifica in formato Heads-Up solo quando ha un'impostazione di priorità alta o massima.

In Android 5.0, è possibile fornire metadati di notifica per aiutare Android ordinare e visualizzare le notifiche in modo più intelligente. Android 5.0 organizza le notifiche in base a priorità, visibilità e categoria.
Le categorie di notifica vengono utilizzate per filtrare le notifiche che possono essere visualizzate quando il dispositivo è in modalità *Non disturbare.*

Per informazioni dettagliate sulla creazione e l'avvio di notifiche con le funzionalità più recenti di Android 5.0, vedere [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="new-apis"></a>Nuove API

Oltre alle nuove funzionalità look-and-feel descritte in precedenza, Android 5.0 aggiunge nuove API che estendono le funzionalità multimediali, di archiviazione e di connettività wireless esistenti. Inoltre, Android 5.0 include nuove API che forniscono il supporto per una nuova funzionalità di pianificazione dei processi.

### <a name="camera"></a>Camera

Android 5.0 offre diverse nuove API per funzionalità avanzate della fotocamera. Il `Android.Hardware.Camera2` nuovo spazio dei nomi include funzionalità per l'accesso ai singoli dispositivi della fotocamera collegati a un dispositivo Android.The new namespace includes functionality for accessing individual camera devices connected to an Android device. Inoltre, `Android.Hardware.Camera2` modella ogni dispositivo della fotocamera come pipeline: accetta una richiesta di acquisizione, acquisisce l'immagine e quindi restituisce il risultato. Questo approccio consente alle app di accodare più richieste di acquisizione a un dispositivo fotocamera.

Le API seguenti rendono possibili queste nuove funzionalità:The following APIs make these new features possible:

- `CameraManager.GetCameraIdList`&ndash; Consente di accedere a livello di codice ai dispositivi della fotocamera; `CameraManager.OpenCamera` per connettersi a un dispositivo specifico.

- `CameraCaptureSession`&ndash; Acquisisce o trasmette immagini dal dispositivo della fotocamera. Implementare `CameraCaptureSession.CaptureListener` un'interfaccia per gestire nuovi eventi di acquisizione delle immagini.

- `CaptureRequest`&ndash; Definisce i parametri di acquisizione.

- `CaptureResult`&ndash; Fornisce i risultati di un'operazione di acquisizione dell'immagine.

Per ulteriori informazioni sulle nuove API della fotocamera in Android 5.0, vedere [Media](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Riproduzione audio

Android 5.0 `AudioTrack` aggiorna la classe per una migliore riproduzione audio:

- `ENCODING_PCM_FLOAT`&ndash; Configura `AudioTrack` per accettare i dati audio in formato a virgola mobile per una migliore gamma dinamica, maggiore headroom e qualità superiore (grazie ad una maggiore precisione). Inoltre, il formato a virgola mobile consente di evitare il ritaglio audio.

- `ByteBuffer`&ndash; È ora possibile fornire `AudioTrack` dati audio come matrice di byte.

- `WRITE_NON_BLOCKING`&ndash; Questa opzione semplifica il buffering e il multithreading per alcune app.

Per ulteriori `AudioTrack` informazioni sui miglioramenti in Android 5.0, vedere [Media](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controllo riproduzione multimediale

Android 5.0 introduce `Android.Media.MediaController` la nuova classe, che sostituisce `RemoteControlClient`. `Android.Media.MediaController`fornisce API di controllo di trasporto semplificate e offre un controllo thread-safe della riproduzione all'esterno del contesto dell'interfaccia utente. Le nuove API seguenti gestiscono il controllo del trasporto:The following new APIs handle transport control:

- `Android.Media.Session.MediaSession`&ndash; Sessione di controllo multimediale che gestisce più controller. Chiama `MediaSession.GetSessionToken` per richiedere un token che l'app usa per interagire con la sessione.

- `MediaController.TransportControls`&ndash; Gestisce i comandi di trasporto quali **Riproduci**, **Interrompi**e **Ignora**.

Inoltre, è possibile `Android.App.Notification.MediaStyle` utilizzare la nuova classe per associare una sessione multimediale al contenuto di notifica avanzata (ad esempio l'estrazione e la visualizzazione della copertina dell'album).

Per ulteriori informazioni sulle nuove funzionalità di controllo della riproduzione multimediale in Android 5.0, consultate [Elementi multimediali.](https://developer.android.com/about/versions/android-5.0.html#Media)

### <a name="storage"></a>Archiviazione

Android 5.0 aggiorna Storage Access Framework per semplificare l'utilizzo delle applicazioni con directory e documenti:

- Per selezionare un sottoalbero di directory, è possibile compilare e inviare una `Android.Intent.Action.OPEN_DOCUMENT_TREE` finalità. Questa finalità fa sì che il sistema per visualizzare tutte le istanze del provider che supportano la selezione del sottoalbero; l'utente quindi sfoglia e seleziona una directory.

- Per creare e gestire nuovi documenti o directory in `CreateDocument`qualsiasi `RenameDocument`punto `DeleteDocument` di `DocumentsContract`un sottoalbero, utilizzare i nuovi metodi , e di .

- Per ottenere i percorsi delle directory multimediali su `Android.Content.Context.GetExternalMediaDirs` tutti i dispositivi di archiviazione condivisi, chiamare il nuovo metodo.

Per altre informazioni sulle nuove API di archiviazione in Android 5.0, vedere [Archiviazione.](https://developer.android.com/preview/api-overview.html#Storage)

### <a name="wireless--connectivity"></a>Connettività & wireless

Android 5.0 aggiunge i seguenti miglioramenti API per il wireless e la connettività:

- Nuove API *multi-rete* che consentono alle app di trovare e selezionare reti con funzionalità specifiche prima di effettuare una connessione.

- Funzionalità di trasmissione Bluetooth che consente a un dispositivo Android 5.0 di agire come periferica Bluetooth a bassa energia.

- Miglioramenti NFC che semplificano l'utilizzo delle funzionalità di comunicazione near-field per la condivisione dei dati con altri dispositivi.

Per ulteriori informazioni sulle nuove API wireless e di connettività in Android 5.0, vedere [Wireless e connettività](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Pianificazione dei processi

Android 5.0 introduce `JobScheduler` una nuova API che consente agli utenti di ridurre al minimo lo scarico della batteria pianificando l'esecuzione di determinate attività solo quando il dispositivo è collegato e in carica. Questa funzionalità di pianificazione dei processi può essere utilizzata anche per pianificare l'esecuzione di un'attività quando le condizioni sono più adatte a tale attività, ad esempio per scaricare un file di grandi dimensioni quando il dispositivo è connesso tramite una rete Wi-Fi anziché una rete a consumo.

Per ulteriori informazioni sulle nuove API di pianificazione dei processi in Android 5.0, vedere [Pianificazione dei processi](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Riepilogo

Questo articolo ha fornito una panoramica delle nuove importanti funzionalità di Android 5.0 per gli sviluppatori di app Xamarin.Android:This article provided an overview of important new features in Android 5.0 for Xamarin.Android app developers:

- Tema Material

- Animations

- Visualizzare ombre e prospetto

- Caratteristiche del colore, come la tinta disegnabile e l'estrazione dei colori prominenti

- Il `RecyclerView` nuovo `CardView` e widget

- Miglioramenti delle notifiche

- Nuove API per fotocamera, riproduzione audio, controllo multimediale, archiviazione, wireless/connettività e pianificazione dei processi

Se non hai familiarità con lo sviluppo di Xamarin Android, leggi Setup and Installation (Configurazione [e installazione)](~/android/get-started/installation/index.md) per iniziare a utilizzare Xamarin.Android.
[Ciao, Android](~/android/get-started/hello-android/index.md) è un'ottima introduzione per imparare a creare progetti Android.

## <a name="related-links"></a>Collegamenti correlati

- [Anteprima sviluppatore Android L](https://developer.android.com/preview/index.html)
- [Scarica l'SDK di Android](https://developer.android.com/sdk/index.html#Other)
- [Progettazione dei materiali](https://developer.android.com/preview/material/index.html)
