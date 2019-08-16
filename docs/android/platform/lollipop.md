---
title: Funzioni Lollipop
description: Questo articolo fornisce una panoramica di alto livello delle nuove funzionalità introdotte in Android 5,0 (Lollipop). Queste funzionalità includono un nuovo stile di interfaccia utente denominato tema materiale, oltre a nuove funzionalità di supporto quali animazioni, ombreggiature di visualizzazione e colorazione disegnabile. Android 5,0 include anche notifiche avanzate, due nuovi widget dell'interfaccia utente, una nuova utilità di pianificazione dei processi e alcune nuove API per migliorare le funzionalità di archiviazione, rete, connettività e multimedia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7cc7eeabebbfbcd4b264202235307638b88842d8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524206"
---
# <a name="lollipop-features"></a>Funzioni Lollipop

_Questo articolo fornisce una panoramica di alto livello delle nuove funzionalità introdotte in Android 5,0 (Lollipop). Queste funzionalità includono un nuovo stile di interfaccia utente denominato tema materiale, oltre a nuove funzionalità di supporto quali animazioni, ombreggiature di visualizzazione e colorazione disegnabile. Android 5,0 include anche notifiche avanzate, due nuovi widget dell'interfaccia utente, una nuova utilità di pianificazione dei processi e alcune nuove API per migliorare le funzionalità di archiviazione, rete, connettività e multimedia._

## <a name="lollipop-overview"></a>Panoramica del simbolo

Android 5,0 (Lollipop) introduce un nuovo linguaggio di progettazione, un *progetto di materiale*ed è dotato di un cast di supporto di nuove funzionalità per rendere le app più semplici e intuitive da usare. Con la progettazione del materiale, Android 5,0 non solo fornisce ai telefoni Android un lift-Lift; fornisce anche un nuovo set di regole di progettazione per tablet basati su Android, computer desktop, orologi e Smart TV. Queste regole di progettazione enfatizzano la semplicità e il minimo sfruttando gli attributi tattili noti, ad esempio la superficie realistica e i segnali perimetrali, per aiutare gli utenti a comprendere in modo rapido e intuitivo l'interfaccia.

Il *tema materiale* è l'incarnazione di questi principi di progettazione dell'interfaccia utente in Android. Questo articolo inizia con la copertura delle funzionalità di supporto del tema del materiale:

- **Animazioni** Animazioni di feedback tocco, animazioni di transizione delle attività, animazioni di transizione dello stato di visualizzazione e un effetto di rivelamento. &ndash;

- **Visualizza ombre ed elevazione** Le visualizzazioni dispongono ora `elevation` di una proprietà. &ndash;   le visualizzazioni con `elevation` valori più elevati hanno il cast di ombre più grandi sullo sfondo.

- **Funzionalità colore** La colorazione disegnatore consente di riutilizzare gli asset delle immagini modificando il colore e l' *estrazione dei colori prominente* consente di eseguire dinamicamente il tema dell'app in base ai colori di un'immagine. &ndash;

Molte funzionalità dei temi materiali sono già integrate nell'esperienza dell'interfaccia utente di Android 5,0, mentre altre devono essere aggiunte in modo esplicito alle app. Ad esempio, alcune visualizzazioni standard, ad esempio i pulsanti, includono già animazioni di feedback tocco, mentre le app devono abilitare la maggior parte delle ombreggiature di visualizzazione.

Oltre ai miglioramenti apportati all'interfaccia utente per quanto riguarda il tema Material, Android 5,0 include anche diverse altre nuove funzionalità descritte in questo articolo:

- **Notifiche avanzate** Le notifiche in Android 5,0 sono state aggiornate in modo significativo con un nuovo aspetto, il supporto per le notifiche di lockscreen e un nuovo formato di presentazione delle notifiche *Heads-up.* &ndash;

- **Nuovi widget dell'interfaccia utente** Il nuovo `RecyclerView` widget rende più semplice per le app fornire set di dati di grandi dimensioni e informazioni complesse e `CardView` il nuovo widget fornisce un formato di presentazione di tipo scheda semplificato per la visualizzazione di testo e immagini. &ndash;

- **Nuove API** &ndash; Android 5,0 aggiunge nuove API per il supporto di più reti, connettività Bluetooth migliorata, gestione dell'archiviazione più semplice e controllo più flessibile dei lettori multimediali e dei dispositivi della fotocamera. È disponibile una nuova funzionalità di pianificazione del processo per eseguire le attività in modo asincrono ai tempi pianificati. Questa funzionalità consente di migliorare la durata della batteria, ad esempio pianificando le attività da eseguire quando il dispositivo viene collegato e caricato.


## <a name="requirements"></a>Requisiti

Per usare le nuove funzionalità di Android 5,0 nelle app basate su Novell, è necessario quanto segue:

- **Novell. Android** &ndash; Novell. Android 4,20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac. 

- **Android SDK** &ndash; Android 5,0 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

- **Java Developer Kit** Novell. Android richiede [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva se si sta sviluppando per livello API 24 o superiore (JDK 1,8 supporta anche i livelli API precedenti a 24, incluso il simbolo). &ndash; La versione a 64 bit di JDK 1,8 è obbligatoria se si usano controlli personalizzati o il Visualizzatore anteprima moduli.

È possibile continuare a usare [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si sta sviluppando in modo specifico per il livello API 23 o versioni precedenti.


## <a name="setting-up-an-android-50-project"></a>Configurazione di un progetto Android 5,0

Per creare un progetto Android 5,0, è necessario installare gli strumenti e i pacchetti SDK più recenti. Usare la procedura seguente per configurare un progetto Novell. Android destinato a Android 5,0:

1. Installare gli strumenti di Novell. Android e attivare la licenza di Novell. Per ulteriori informazioni sull'installazione di Novell. Android, vedere [configurazione e installazione](~/android/get-started/installation/index.md) .

2. Se si usa Visual Studio per Mac, installare gli aggiornamenti più recenti di Android 5,0.

3. Avviare Gestione Android SDK (in Visual Studio per Mac usare **gli strumenti &gt; &hellip;Apri Android SDK Manager**) e installare Android SDK Tools 23.0.5 o versione successiva:

    [![Selezione di strumenti Android SDK in gestione Android SDK](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Installare anche i pacchetti più recenti di Android 5,0 SDK (API 21 o versione successiva):

    [![Installazione dei pacchetti SDK di Android 5,0 in gestione Android SDK](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Per ulteriori informazioni sull'utilizzo di gestione Android SDK, vedere [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Creare un nuovo progetto Novell. Android. Se non si ha familiarità con lo sviluppo per Android con Novell, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti Android. Quando si crea un progetto Android, assicurarsi di configurare le impostazioni di versione per Android 5,0.
   In Visual Studio per Mac passare a **Opzioni &gt; progetto compila &gt; generale** e impostare Framework di **destinazione** su **Android 5,0 (Lollipop)** o versione successiva:

    ![Impostazione del Framwework di destinazione su Android 5,0 Lollipop](lollipop-images/target-framework.png)

   In  **&gt; opzioni progetto compilare &gt; un'applicazione Android**, impostare la versione minima e di destinazione di Android su **automatico: usare la versione del Framework di destinazione**:

    ![Impostazione delle versioni di Android minime e di destinazione su automatico](lollipop-images/minimum-android-version.png)

5. Configurare un emulatore o un dispositivo Android per testare l'app. Se si usa un emulatore, vedere [emulatore Android installazione](~/android/get-started/installation/android-emulator/index.md) per informazioni su come configurare un emulatore Android da usare con Xamarin Studio o Visual Studio. Se si usa un dispositivo Android, vedere [la pagina relativa alla configurazione dell'SDK di anteprima](https://developer.android.com/preview/setup-sdk.html) per informazioni su come aggiornare il dispositivo per Android 5,0. Per configurare il dispositivo Android per l'esecuzione e il debug di applicazioni Novell. Android, vedere [configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Se si sta aggiornando un progetto Android esistente destinato a Android L Preview, è necessario aggiornare il Framework di **destinazione** e la **versione di Android** ai valori descritti in precedenza.

## <a name="important-changes"></a>Modifiche importanti

Le app Android pubblicate in precedenza potrebbero essere interessate dalle modifiche apportate in Android 5,0. In particolare, Android 5,0 usa un nuovo runtime e un formato di notifica notevolmente modificato.

### <a name="android-runtime"></a>Runtime di Android

Android 5,0 usa il nuovo runtime di Android (ART) come runtime predefinito anziché Dalvik. ART implementa diverse nuove funzionalità principali:

- **Compilazione AOT (Ahead of Time)** &ndash; AOT può migliorare le prestazioni dell'app compilando il codice dell'app prima che l'app venga avviata per la prima volta. Quando viene installata un'app, l'arte genera un file eseguibile dell'app compilato per il dispositivo di destinazione.

- **Garbage Collection migliorata (GC)** &ndash; I miglioramenti della GC nell'arte possono anche migliorare le prestazioni dell'app. Garbage Collection ora usa una pausa GC anziché due e le operazioni GC simultanee vengono completate in modo più tempestivo.

- **Debug delle app migliorato** &ndash; Art fornisce un maggior numero di dettagli diagnostici per l'analisi delle eccezioni e dei report di arresto anomalo.

Le app esistenti dovrebbero funzionare senza modifiche in &ndash; arte, ad eccezione delle app che sfruttano le tecniche univoche del precedente Runtime Dalvik, che potrebbe non funzionare in arte. Per altre informazioni su queste modifiche, vedere [Verifica del comportamento delle app in Android Runtime (Art)](https://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Modifiche delle notifiche

Le notifiche sono state modificate in modo significativo in Android 5,0:

- **I suoni e le vibrazioni vengono gestiti in modo diverso** `Ringtone` `Notification.Builder` `Vibrator`I suoni e le vibrazioni delle notifiche vengono ora gestiti da anziché `MediaPlayer`, e. &ndash;

- **Nuova combinazione colori** &ndash; In base al tema Material, le notifiche vengono visualizzate con testo scuro su sfondi bianco o molto chiaro. Inoltre, i canali alpha nelle icone di notifica possono essere modificati da Android per coordinarsi con le combinazioni di colori del sistema. 

- **Notifiche di lockscreen** &ndash; È ora possibile visualizzare le notifiche nel dispositivo lockscreen.

- **Intestazioni** &ndash; Le notifiche ad alta priorità vengono ora visualizzate in una finestra mobile di piccole dimensioni (notifica di verifica) quando il dispositivo viene sbloccato e la schermata è accesa.

Nella maggior parte dei casi, la funzionalità di notifica delle app esistente a Android 5,0 richiede i passaggi seguenti:

1. Convertire il codice in modo `Notification.Builder` da usare `NotificationsCompat.Builder`(o) per la creazione di notifiche. 

2. Verificare che gli asset di notifica esistenti siano visualizzabili nella nuova combinazione di colori del tema Material.

3. Decidere quale visibilità deve avere le notifiche quando vengono presentate in lockscreen. Se una notifica non è pubblica, quale contenuto dovrebbe essere visualizzato in lockscreen?

4. Impostare la categoria delle notifiche in modo che vengano gestite correttamente nella nuova modalità Android 5,0 non disturbare.

Se le notifiche presentano controlli di trasporto, visualizzare lo stato della riproduzione `RemoteControlClient`multimediale, usare `ActivityManager.GetRecentTasks`o chiamare, vedere [modifiche di comportamento importanti](https://developer.android.com/preview/api-overview.html#Behaviors) per altre informazioni sull'aggiornamento delle notifiche per Android 5,0.

Per informazioni sulla creazione di notifiche in Android, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Tema Material

Il nuovo tema del materiale Android 5,0 introduce modifiche apportate all'aspetto dell'interfaccia utente di Android. Gli elementi visivi ora utilizzano superfici tattili che accettano grafici in grassetto, tipografia e colori luminosi della progettazione basata su stampa. Negli screenshot seguenti vengono illustrati alcuni esempi di tema del materiale:

[![Screenshot della schermata iniziale del tema Material, della schermata delle app e della schermata di impostazione](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5,0 accoglie la schermata iniziale visualizzata a sinistra. Lo screenshot centrale è la prima schermata dell'elenco di app e lo screenshot a destra è la schermata **Impostazioni** . La specifica di [progettazione materiale](https://material.io/guidelines/material-design/introduction.html) di Google illustra le regole di progettazione sottostanti rispetto al nuovo concetto di tema di materiali.

Il tema materiale include tre versioni predefinite che è possibile usare nell'app: il `Theme.Material` tema scuro (impostazione predefinita), il `Theme.Material.Light` tema e il `Theme.Material.Light.DarkActionBar` tema: 

[![Screenshot dei temi Dark, Light e DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Per altre informazioni sull'uso delle funzionalità del tema materiale nelle app Novell. Android, vedere [tema del materiale](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animations

Android 5,0 fornisce animazioni di feedback tocco, animazioni di transizione delle attività e animazioni di transizione dello stato di visualizzazione per rendere più intuitive le interfacce delle app. Inoltre, le app Android 5,0 possono usare le animazioni per *rivelare gli effetti* per nascondere o rivelare le visualizzazioni. È possibile usare le impostazioni di *movimento curvo* per configurare la modalità di rendering delle animazioni in modo rapido o lento.


### <a name="touch-feedback-animations"></a>Animazioni feedback tocco

Le animazioni con feedback tocco consentono agli utenti di ricevere commenti e suggerimenti visivi quando è stata toccata una vista. Ad esempio, i pulsanti ora visualizzano un effetto ondulato quando vengono &ndash; modificati. si tratta dell'animazione di feedback tocco predefinita in Android 5,0. L'animazione Ripple viene implementata dalla `RippleDrawable` nuova classe. L'effetto Ripple può essere configurato per terminare in corrispondenza dei limiti della visualizzazione o estendersi oltre i limiti della visualizzazione. Ad esempio, la sequenza di screenshot seguente illustra l'effetto Ripple in un pulsante durante l'animazione tocco:

![Schermate frame per fotogramma di animazione Ripple su un pulsante](lollipop-images/touch-animation.png)

Il contatto iniziale con il pulsante si trova nella prima immagine a sinistra, mentre la sequenza rimanente, da sinistra a destra, illustra il modo in cui l'effetto Ripple viene distribuito al bordo del pulsante. Quando l'animazione Ripple termina, la visualizzazione torna all'aspetto originale. L'animazione Ripple predefinita viene svolta in una frazione di secondo, ma la lunghezza dell'animazione può essere personalizzata per lunghezze di tempo più lunghe o più brevi.

Per altre informazioni sulle animazioni di feedback tocco in Android 5,0, vedere [personalizzare il feedback del tocco](https://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Animazioni di transizione attività

Le animazioni di transizione delle attività offrono agli utenti un senso di continuità visiva quando un'attività esegue la transizione a un'altra. Le app possono specificare tre tipi di animazioni di transizione:

- **Immettere la transizione** &ndash; Quando un'attività entra nella scena.

- **Transizione di uscita** &ndash; Quando un'attività esce dalla scena.

- **Transizione di elementi condivisi** &ndash; Quando viene modificata una vista comune a due attività quando la prima attività esegue la transizione a quella successiva.

Ad esempio, la sequenza di screenshot seguente illustra una transizione di elemento condiviso:

[![Schermate frame per fotogramma di un'animazione di transizione di elemento condiviso](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento condiviso (foto di un bruco) è una delle diverse visualizzazioni nella prima attività. si ingrandisce in modo che diventi l'unica visualizzazione nella seconda attività quando la prima attività passa alla seconda.

#### <a name="enter-transition-animation-types"></a>Immettere i tipi di animazione della transizione

Per le transizioni Enter, Android 5,0 fornisce tre tipi di animazioni:

- **Esplodi animazione** &ndash; Ingrandisce una visualizzazione dal centro della scena.

- **Animazione diapositiva** &ndash; Sposta una visualizzazione in da uno dei bordi di una scena.

- **Animazione Fade** &ndash; Dissolve una visualizzazione nella scena.

#### <a name="exit-transition-animation-types"></a>Tipi di animazione di transizione Exit

Per le transizioni di uscita, Android 5,0 fornisce tre tipi di animazioni:

- **Esplodi animazione** &ndash; Compatta una visualizzazione al centro della scena.

- **Animazione diapositiva** &ndash; Sposta una visualizzazione in uno dei bordi di una scena.

- **Animazione Fade** &ndash; Dissolve una visualizzazione fuori dalla scena.

#### <a name="shared-element-transition-animation-types"></a>Tipi di animazione di transizioni di elementi condivisi

Le transizioni di elementi condivisi supportano più tipi di animazioni, ad esempio:

- Modifica del layout o dei limiti di ritaglio di una visualizzazione.

- Modificare la scala e la rotazione di una visualizzazione.

- Modifica delle dimensioni e del tipo di scala per una visualizzazione.

Per altre informazioni sulle animazioni di transizione delle attività in Android 5,0, vedere Personalizzare le transizioni di [attività](https://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animazioni di transizione dello stato di visualizzazione

Android 5,0 rende possibile l'esecuzione delle animazioni quando cambia lo stato di una vista. È possibile animare le transizioni di stato di visualizzazione utilizzando una delle tecniche seguenti:

- Creare drawables che animano le modifiche di stato associate a una visualizzazione specifica. La nuova `AnimatedStateListDrawable` classe consente di creare drawables per la visualizzazione delle animazioni tra le modifiche dello stato di visualizzazione.

- Definire la funzionalità di animazione che viene eseguita quando cambia lo stato di una vista. La nuova `StateListAnimator` classe consente di definire un animatore che viene eseguito quando lo stato di una visualizzazione viene modificato.

Per altre informazioni sulle animazioni di transizione dello stato di visualizzazione in Android 5,0, vedere [animazione delle modifiche allo stato di visualizzazione](https://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Rivela effetto

L' *effetto Reveal* è un cerchio di ridimensionamento che modifica il raggio per rivelare o nascondere una visualizzazione. È possibile controllare questo effetto impostando il raggio iniziale e finale del cerchio di ritaglio. La sequenza di screenshot seguente illustra l'animazione di un effetto rivela dal centro dello schermo:

[![Schermate frame per fotogramma dell'animazione Reveal](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

Nella sequenza successiva viene illustrata un'animazione per rivelare effetti che si verifica dall'angolo inferiore sinistro dello schermo:

[![Schermate frame per frame dell'animazione di ritaglio](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Le animazioni Reveal possono essere annullate. ovvero, il cerchio di ritaglio può ridursi per nascondere la visualizzazione anziché ingrandirla per rivelare la visualizzazione.

Per altre informazioni sull'effetto di rivelamento di Android 5,0 in, vedere [usare l'effetto Reveal](https://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Movimento curvo

Oltre a queste funzionalità di animazione, Android 5,0 fornisce anche nuove API che consentono di specificare le curve temporali e di movimento delle animazioni. Android 5,0 usa queste curve per interpolare lo spostamento temporale e spaziale durante le animazioni. In Android 5,0 sono definite tre curve:

- **Fast\_out\_Linearin\_** accelera&ndash; rapidamente e continua ad accelerare fino alla fine dell'animazione.

- **\_Il rallentamentorapido\_in accelera rapidamente e lentamente rallenta verso la fine dell'animazione.\_** &ndash;

- **Linear\_out\_Slowin\_** inizia&ndash; con una velocità massima e rallenta lentamente fino alla fine dell'animazione.

È possibile utilizzare la nuova `PathInterpolator` classe per specificare come avviene l'interpolazione di movimento. `PathInterpolator`è un interpolatore che attraversa i percorsi di animazione in base ai punti di controllo e alle curve di movimento specificati. Per altre informazioni su come specificare le impostazioni di movimento curvo in Android 5,0, vedere [usare un movimento curvo](https://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Visualizzare le ombreggiature & elevazione

In Android 5,0 è possibile specificare l' *elevazione* di una vista impostando una nuova `Z` proprietà. Un valore `Z` maggiore determina il cast di un'ombreggiatura maggiore sullo sfondo, rendendo la visualizzazione più alta rispetto allo sfondo. È possibile impostare l'elevazione iniziale di una visualizzazione configurando il relativo `elevation` attributo nel layout.

Nell'esempio seguente vengono illustrate le ombre sottoposte `TextView` a cast da un controllo vuoto quando il relativo attributo Elevation è impostato rispettivamente su 2DP, 4DP e 6DP:

[![Schermate delle ombre di visualizzazione raffreddata più grandi](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Le impostazioni dell'ombreggiatura di visualizzazione possono essere statiche (come illustrato in precedenza) oppure possono essere usate nelle animazioni per far sembrare che una visualizzazione si alzi temporaneamente al di sopra dello sfondo della visualizzazione. È possibile utilizzare la `ViewPropertyAnimator` classe per animare l'elevazione di una visualizzazione. L'elevazione di una vista è la somma della relativa `elevation` impostazione di layout `translationZ` più una proprietà che è possibile impostare `ViewPropertyAnimator` tramite una chiamata al metodo.

Per altre informazioni su come visualizzare le ombreggiature in Android 5,0, vedere [definizione di ombreggiature e visualizzazioni](https://developer.android.com/training/material/shadows-clipping.html)di ritaglio.


## <a name="color-features"></a>Funzionalità colore

Android 5,0 fornisce due nuove funzionalità per la gestione dei colori nelle app:

- La *colorazione disegnatore* consente di modificare i colori delle risorse dell'immagine modificando un attributo di layout.

- L' *estrazione dei colori prominente* consente di personalizzare dinamicamente il tema colori dell'app per coordinarsi con la tavolozza dei colori di un'immagine visualizzata.


### <a name="drawable-tinting"></a>Colorazione ricavabile

I layout Android 5,0 riconoscono `tint` un nuovo attributo che è possibile usare per impostare il colore di drawables senza dover creare più versioni di questi asset per visualizzare colori diversi. Per usare questa funzionalità, è necessario definire una bitmap come maschera alfa e usare l' `tint` attributo per definire il colore dell'asset. In questo modo è possibile creare asset una sola volta e colorarli nel layout in modo che corrispondano al tema.

Nell'esempio seguente, per creare variazioni di tinta, viene usato un logo di &ndash; immagine &ndash; singolo con uno sfondo trasparente:

![Logo Novell bianco con sfondo trasparente](lollipop-images/xamarin-logo-white.png)

Questo logo viene visualizzato sopra uno sfondo circolare blu, come illustrato negli esempi seguenti. L'immagine a sinistra è il modo in cui il logo viene `tint` visualizzato senza un'impostazione. Nell'immagine centrale, l' `tint` attributo del logo è impostato su un grigio scuro. Nell'immagine a destra `tint` è impostato su un grigio chiaro:

![Esempi del logo precedente con impostazioni di tinta diverse](lollipop-images/drawable-tinting.png)

Per altre informazioni sulla colorazione delineabile in Android 5,0, vedere la pagina relativa alla [colorazione](https://developer.android.com/training/material/drawables.html#DrawableTint)ricavabile.


### <a name="prominent-color-extraction"></a>Estrazione di colori prominente

La nuova classe Android `Palette` 5,0 consente di estrarre i colori da un'immagine in modo che sia possibile applicarli dinamicamente a una tavolozza dei colori personalizzata. La `Palette` classe estrae sei colori da un'immagine ed etichetta questi colori in base ai relativi livelli di saturazione e luminosità dei colori:

- Vibrante

- Scuro intenso

- Luce vibrante

- Disattivato

- Disattivato scuro

- Luce silenziata

Negli screenshot seguenti, ad esempio, un'app di visualizzazione foto estrae i colori prominenti dall'immagine visualizzata e usa questi colori per adattare la combinazione di colori dell'app in modo che corrisponda all'immagine:

[![Screenshot delle estrazioni dei colori del tema verde, rosa e blu](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Nelle schermate precedenti la barra delle azioni è impostata sul colore "vivace chiaro" Estratto e lo sfondo è impostato sul colore "vivace scuro" Estratto. In ogni esempio precedente viene inclusa una riga di quadratini di colore piccolo per illustrare i colori della tavolozza estratti dall'immagine.

Per altre informazioni sull'estrazione dei colori in Android 5,0, vedere [estrazione di colori prominenti da un'immagine](https://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Nuovi widget dell'interfaccia utente

Android 5,0 introduce due nuovi widget dell'interfaccia utente:

- `RecyclerView`&ndash; Gruppo di visualizzazioni che visualizza un elenco di elementi scorrevoli.

- `CardView`&ndash; Layout di base con angoli arrotondati.

Entrambi i widget includono il supporto cotto per le funzionalità del tema Material; `RecyclerView` USA ad esempio le animazioni per l'aggiunta e la rimozione delle `CardView` visualizzazioni e usa le ombreggiature delle visualizzazioni per far apparire ogni scheda come float sopra lo sfondo. Gli esempi di questi nuovi widget sono illustrati negli screenshot seguenti:

[![Screenshot delle app compilate con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La schermata a sinistra è un esempio di `RecyclerView` come usato in un'app di posta elettronica e la schermata a destra è un esempio di `CardView` come usato in un'app di prenotazione viaggi.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView`è simile a `ListView,` ma è più adatto per set di viste o elenchi di grandi dimensioni con elementi che cambiano dinamicamente. Analogamente a come `ListView,` si specifica un adapter per accedere al set di dati sottostante. Tuttavia, a differenza `ListView,` di quando si usa un *gestore di layout* per `RecyclerView`posizionare gli elementi all'interno di. Il gestore del layout si occupa anche del riciclo delle visualizzazioni; gestisce il riutilizzo delle visualizzazioni di elementi che non sono più visibili all'utente.

Quando si usa un `RecyclerView` widget, è necessario specificare un `LayoutManager` e un adapter. Come illustrato in questa figura, `LayoutManager` è l'intermediario tra la scheda `RecyclerView`e:

![Diagramma di RecyclerView con supporto per LayoutManager, adapter e DataSet](lollipop-images/recyclerview-diagram.png)

Gli screenshot seguenti illustrano un `RecyclerView` oggetto che contiene 100 elementi (ogni elemento è costituito da un `TextView` `ImageView` e un):

[![Screenshot di un'app RecyclerView che scorre le immagini](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`gestisce questo set di dati di grandi &ndash; dimensioni con scorrimento semplice dall'inizio dell'elenco fino alla fine dell'elenco in questa applicazione di esempio richiede solo pochi secondi. `RecyclerView`supporta inoltre animazioni; di fatto, le animazioni per l'aggiunta e la rimozione di elementi sono abilitate per impostazione predefinita. Quando un elemento viene aggiunto a un `RecyclerView`, si dissolve in come illustrato in questa sequenza di schermate:

[![Schermata cornice per cornice di un elemento foto che si dissolve](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Per ulteriori informazioni `RecyclerView`su, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>CardView

`CardView`è una visualizzazione semplice che simula una scheda Mobile con angoli arrotondati. Poiché `CardView` dispone di ombre di visualizzazione predefinite, fornisce un modo semplice per aggiungere profondità visiva all'app. Gli screenshot seguenti illustrano tre esempi orientati al testo `CardView`di:

[![Screenshot di esempio di app che usano RecyclerView con elementi basati su CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Ognuna delle schede nell'esempio precedente contiene un oggetto `TextView`, il colore di sfondo viene impostato tramite l' `cardBackgroundColor` attributo.

Per ulteriori informazioni `CardView`su, vedere [CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notifiche avanzate

Il sistema di notifica in Android 5,0 è stato aggiornato in modo significativo con un nuovo formato visivo e nuove funzionalità. Le notifiche hanno un nuovo aspetto in Android 5,0. Ad esempio, le notifiche in Android 5,0 usano ora testo scuro su uno sfondo chiaro:

![Esempio di una notifica Android 5,0 non espansa](lollipop-images/expanded-notification-contracted.png)

Quando viene visualizzata un'icona di grandi dimensioni in una notifica (come illustrato nell'esempio precedente), Android 5,0 presenta l'icona piccola come un badge sull'icona grande. 

In Android 5,0, le notifiche possono essere visualizzate anche sul dispositivo lockscreen.
Ecco ad esempio una schermata di esempio di un lockscreen con una sola notifica:

[![Screenshot della notifica visualizzata nella schermata di blocco](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Gli utenti possono fare doppio toccare una notifica in LockScreen per sbloccare il dispositivo e passare all'app che ha originato tale notifica oppure scorrere la notifica per ignorarla. Le notifiche hanno una nuova impostazione di *visibilità* che determina la quantità di contenuto che è possibile visualizzare nella schermata di blocco. Gli utenti possono scegliere se consentire il contenuto sensibile da visualizzare nelle notifiche di lockscreen.

Android 5,0 introduce un nuovo formato di presentazione delle notifiche con priorità alta chiamato *Heads-up*. Le notifiche Heads-up scorrono verso il basso dalla parte superiore della schermata per alcuni secondi e quindi ritirate alla sfumatura della notifica nella parte superiore della schermata. Le notifiche relative alle intestazioni consentono all'interfaccia utente del sistema di inserire informazioni importanti davanti all'utente senza compromettere l'attività attualmente in esecuzione. Nell'esempio seguente viene illustrata una semplice notifica Heads-up visualizzata all'inizio di un'app:

[![Esempio di una notifica delle intestazioni](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Le notifiche Heads-up vengono in genere usate per gli eventi seguenti:

- Un nuovo messaggio successivo

- Telefonata in arrivo

- Indicazione batteria insufficiente

- Un allarme

Android 5,0 Visualizza una notifica nel formato Heads-up solo quando ha un'impostazione di priorità alta o massima.

In Android 5,0 è possibile fornire i metadati delle notifiche per aiutare Android a ordinare e visualizzare le notifiche in modo più intelligente. Android 5,0 organizza le notifiche in base a priorità, visibilità e categoria.
Le categorie di notifiche vengono usate per filtrare le notifiche che possono essere presentate quando il dispositivo è in modalità *non disturbare* .

Per informazioni dettagliate sulla creazione e l'avvio di notifiche con le funzionalità più recenti di Android 5,0, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Nuove API

Oltre alle nuove funzionalità di ricerca descritte sopra, Android 5,0 aggiunge nuove API che estendono le funzionalità della funzionalità di connettività, archiviazione e rete multimediale esistente. Android 5,0 include inoltre nuove API che forniscono supporto per una nuova funzionalità dell'utilità di pianificazione dei processi.

### <a name="camera"></a>Fotocamera

Android 5,0 fornisce diverse nuove API per migliorare le funzionalità della fotocamera. Il nuovo `Android.Hardware.Camera2` spazio dei nomi include funzionalità per l'accesso ai singoli dispositivi della fotocamera connessi a un dispositivo Android. Inoltre, `Android.Hardware.Camera2` modella ogni dispositivo della fotocamera come una pipeline: accetta una richiesta di acquisizione, acquisisce l'immagine e quindi restituisce il risultato. Questo approccio consente alle app di accodare più richieste di acquisizione a un dispositivo fotocamera.

Le API seguenti rendono possibili queste nuove funzionalità:

- `CameraManager.GetCameraIdList`Consente di accedere a livello di codice ai dispositivi della fotocamera `CameraManager.OpenCamera` , usando per connettersi a un dispositivo della fotocamera specifico. &ndash;

- `CameraCaptureSession`&ndash; Acquisisce o trasmette immagini dal dispositivo della fotocamera. Implementare un' `CameraCaptureSession.CaptureListener` interfaccia per gestire i nuovi eventi di acquisizione di immagini.

- `CaptureRequest`&ndash; Definisce i parametri di acquisizione.

- `CaptureResult`&ndash; Fornisce i risultati di un'operazione di acquisizione dell'immagine.

Per altre informazioni sulle nuove API della fotocamera in Android 5,0, vedere [supporti](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Riproduzione audio

Android 5,0 aggiorna la `AudioTrack` classe per una migliore riproduzione audio:

- `ENCODING_PCM_FLOAT`&ndash; Configurainmododaaccettareidatiaudioinformatoavirgolamobileperunintervallodinamicomigliore,unamaggiorecapacitàeunaqualitàsuperiore(grazieauna`AudioTrack` maggiore precisione). Il formato a virgola mobile consente inoltre di evitare il ritaglio audio.

- `ByteBuffer`È ora possibile fornire dati audio a `AudioTrack` come matrice di byte. &ndash;

- `WRITE_NON_BLOCKING`&ndash; Questa opzione semplifica il buffering e il multithreading per alcune app.

Per ulteriori informazioni `AudioTrack` sui miglioramenti apportati a Android 5,0, vedere [supporti](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controllo riproduzione multimediale

Android 5,0 introduce la nuova `Android.Media.MediaController` classe, che sostituisce `RemoteControlClient`. `Android.Media.MediaController`fornisce API di controllo del trasporto semplificate e offre il controllo thread-safe della riproduzione all'esterno del contesto dell'interfaccia utente. Le nuove API seguenti gestiscono il controllo di trasporto:

- `Android.Media.Session.MediaSession`&ndash; Una sessione di controllo multimediale che gestisce più controller. Si chiama `MediaSession.GetSessionToken` per richiedere un token usato dall'app per interagire con la sessione.

- `MediaController.TransportControls`Gestisce i comandi di trasporto, ad esempio **Play**, **Stop**e **Skip.** &ndash;

Inoltre, è possibile usare la nuova `Android.App.Notification.MediaStyle` classe per associare una sessione multimediale con contenuto di notifica avanzato, ad esempio l'estrazione e la visualizzazione di album.

Per altre informazioni sulle nuove funzionalità di controllo della riproduzione multimediale in Android 5,0, vedere [supporti](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Archiviazione

Android 5,0 aggiorna il Framework di accesso alle archiviazione per semplificare l'uso delle applicazioni con directory e documenti:

- Per selezionare un sottoalbero di directory, è possibile compilare e `Android.Intent.Action.OPEN_DOCUMENT_TREE` inviare uno scopo. Questa finalità determina la visualizzazione da parte del sistema di tutte le istanze del provider che supportano la selezione del sottoalbero. l'utente quindi Cerca e seleziona una directory.

- Per creare e gestire nuovi documenti o directory in qualsiasi punto di un sottoalbero, usare `CreateDocument`i `RenameDocument`nuovi metodi `DeleteDocument` , e `DocumentsContract`di.

- Per ottenere i percorsi delle directory multimediali in tutti i dispositivi di archiviazione condivisi, chiamare `Android.Content.Context.GetExternalMediaDirs` il nuovo metodo.

Per altre informazioni sulle nuove API di archiviazione in Android 5,0, vedere [archiviazione](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Connettività & wireless

Android 5,0 aggiunge i miglioramenti apportati alle API seguenti per la connettività e wireless:

- Nuove API multirete che consentono alle app di individuare e selezionare le reti con funzionalità specifiche prima di stabilire una connessione.

- Funzionalità di broadcast Bluetooth che consente a un dispositivo Android 5,0 di agire come periferica Bluetooth a basso consumo.

- Miglioramenti NFC che semplificano l'uso delle funzionalità di comunicazione Near Field per la condivisione di dati con altri dispositivi.

Per altre informazioni sulle nuove API wireless e di connettività in Android 5,0, vedere [wireless e connettività](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Pianificazione di processi

Android 5,0 introduce una nuova `JobScheduler` API che consente agli utenti di ridurre al minimo lo svuotamento della batteria pianificando l'esecuzione di determinate attività solo quando il dispositivo è collegato e caricato. Questa funzionalità dell'utilità di pianificazione dei processi può essere usata anche per pianificare un'attività da eseguire quando le condizioni sono più adatte a tale attività, ad esempio il download di un file di grandi dimensioni quando il dispositivo è connesso tramite una rete Wi-Fi anziché una rete a consumo.

Per altre informazioni sulle nuove API di pianificazione dei processi in Android 5,0, vedere [pianificazione di processi](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Riepilogo

Questo articolo fornisce una panoramica delle nuove funzionalità importanti di Android 5,0 per gli sviluppatori di app Novell. Android:

- Tema Material

- Animations

- Visualizza ombre ed elevazione

- Caratteristiche cromatiche, ad esempio la colorazione e l'estrazione di colori prominenti

- Nuovi `RecyclerView` widget e `CardView`

- Miglioramenti delle notifiche

- Nuove API per fotocamere, riproduzione audio, controllo multimediale, archiviazione, wireless/connettività e pianificazione dei processi

Se non si ha familiarità con lo sviluppo di Novell Android, vedere [installazione e installazione](~/android/get-started/installation/index.md) per iniziare a usare Novell. Android.
[Hello, Android](~/android/get-started/hello-android/index.md) è un'ottima introduzione per imparare a creare progetti Android.



## <a name="related-links"></a>Collegamenti correlati

- [Android L Developer Preview](https://developer.android.com/preview/index.html)
- [Ottenere il Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Progettazione materiale](https://developer.android.com/preview/material/index.html)
- [Principi di progettazione del materiale](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
