---
title: Configurazione di Android SDK per Xamarin.Android
description: Visual Studio include Android SDK Manager che consente di scaricare gli strumenti, le piattaforme e gli altri componenti di Android SDK necessari per sviluppare app Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: f4ff43ea51dc690daa716e69021f690511cdcc97
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854054"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configurazione di Android SDK per Xamarin.Android

_Visual Studio include Android SDK Manager che consente di scaricare gli strumenti, le piattaforme e gli altri componenti di Android SDK necessari per sviluppare app Xamarin.Android._

## <a name="overview"></a>Panoramica

Questa guida illustra come usare Xamarin Android SDK Manager in Visual Studio e Visual Studio per Mac.

> [!NOTE]
> Questa guida si applica a Visual Studio 2019, Visual Studio 2017 e Visual Studio per Mac.  

Xamarin Android SDK Manager viene installato come componente del carico di lavoro**Sviluppo di app per dispositivi mobili con .NET** e consente di scaricare i componenti Android più recenti necessari per lo sviluppo dell'app Xamarin.Android. Sostituisce lo strumento autonomo SDK Manager di Google, che è stato deprecato.

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

## <a name="requirements"></a>Requisiti

Per usare Xamarin Android SDK Manager sono necessari i componenti seguenti:

- Visual Studio 2019 Community, Professional o Enterprise.

- OPPURE Visual Studio 2017 (edizione Community, Professional o Enterprise). È necessario Visual Studio 2017 versione 15.7 o versione successiva.

- Strumenti di Visual Studio per Xamarin versione 4.10.0 o successiva (installato come parte del carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**). 

In Xamarin Android SDK Manager è anche necessario Java Development Kit, che viene installato automaticamente con Xamarin.Android. Sono disponibili numerose alternative di JDK:

-   Per impostazione predefinita Xamarin.Android usa [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), versione necessaria per sviluppare il livello API 24 o un livello superiore. JDK 8 supporta anche i livelli API precedenti al livello 24.

-   È possibile continuare a usare [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) per attività di sviluppo specifiche per il livello API 23 o un livello precedente.

-   Se si usa Visual Studio 15.8 Preview 5 o versioni successive, è possibile provare a usare la [distribuzione Microsoft di Mobile OpenJDK](openjdk.md) (attualmente in anteprima) invece di JDK 8.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

 
## <a name="sdk-manager"></a>SDK Manager 

Per avviare SDK Manager in Visual Studio, selezionare **Strumenti -> Android -> Android SDK Manager**:

[![L[Percorso della voce di menu Android SDK Manager](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

Android SDK Manager si apre nella schermata **Android SDK e strumenti Android**, in cui sono disponibili due schede &ndash; **Piattaforme** e **Strumenti**:

[![S[Screenshot di Android SDK Manager aperto nella scheda Piattaforme](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

La schermata **Android SDK e strumenti Android** viene descritta più nel dettaglio nelle sezioni seguenti.


### <a name="android-sdk-location"></a>Posizione di Android SDK

La posizione di Android SDK è configurata nella parte superiore della schermata **Android SDK e strumenti Android**, come illustrato nella schermata precedente. Questo percorso deve essere configurato correttamente per l'esatto funzionamento delle schede **Piattaforme** e **Strumenti**. È possibile dover impostare il percorso di Android SDK per uno o più dei motivi seguenti:

1. Android SDK Manager non ha rilevato Android SDK. 

2. Android SDK è stato installato in un percorso diverso (percorso non predefinito). 

Per impostare la posizione di Android SDK, fare clic sui puntini di sospensione (&hellip;) a destra di **Percorso di Android SDK**. Si aprirà la finestra di dialogo **Sfoglia per cartelle** da usare per spostarsi nel percorso di Android SDK. Nella schermata seguente Android SDK viene selezionato in **Programmi (x86)\\Android**:

![Schermata della finestra di dialogo Sfoglia per cartelle di Windows in cui si trova android sdk](android-sdk-images/win/05-browse-for-folder.png)

Quando si fa clic su **OK**, SDK Manager gestisce Android SDK installato nella posizione selezionata.


### <a name="tools-tab"></a>Scheda Strumenti

Nella scheda **Strumenti** viene visualizzato un elenco di _strumenti_ e _funzionalità aggiuntive_. Usare questa scheda per installare gli strumenti di Android SDK, gli strumenti della piattaforma e gli strumenti di compilazione.
È anche possibile installare l'emulatore Android, il debugger di basso livello, NDK, l'accelerazione HAXM e le librerie di Google Play.


Ad esempio, per scaricare il pacchetto relativo all'emulatore Android di Google, selezionare il segno di spunta accanto a **Emulatore Android** e fare clic sul pulsante **Applica modifiche**:

[![I[Installazione dell'emulatore Android dalla scheda Strumenti](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

È possibile che venga visualizzato il messaggio _Prima di installare il pacchetto seguente è necessario accettarne le condizioni di licenza_:

![Schermata Accettazione della licenza](android-sdk-images/win/07-license-acceptance.png)

Selezionare **Accetto** se si accettano i termini e le condizioni. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine dell'installazione, nella scheda **Strumenti** saranno visualizzati gli strumenti e le funzionalità aggiuntive installati.

### <a name="platforms-tab"></a>Scheda Piattaforme

Nella scheda **Piattaforme** viene visualizzato un elenco delle versioni SDK della piattaforma insieme ad altre risorse (ad esempio, le immagini del sistema) per ogni piattaforma:

[![S[Schermata del riquadro Piattaforme](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

In questa schermata vengono elencati la versione Android (ad esempio **Android 8.0**), il nome in codice (**Oreo**), il livello API (ad esempio **26**) e le dimensioni dei componenti per la piattaforma (ad esempio **1 GB**). La scheda **Piattaforme** consente di installare componenti per il livello API Android previsto come destinazione. Per altre informazioni sulle versioni di Android e i livelli API, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android).

Quando tutti i componenti di una piattaforma sono installati, il nome della piattaforma viene affiancato da un segno di spunta. Se non tutti i componenti di una piattaforma sono installati, la casella per la piattaforma è compilata. Fare clic sulla casella **+** a sinistra della piattaforma per espandere una piattaforma e visualizzare i relativi componenti e i componenti installati.
Fare clic su **-** per comprimere l'elenco dei componenti di una piattaforma.

Per aggiungere un'altra piattaforma a SDK, selezionare la casella accanto alla piattaforma finché viene visualizzato il segno di spunta per installare tutti i relativi componenti, quindi fare clic su **Applica modifiche**:

[![E[Esempio di aggiunta dei componenti di Android Nougat 7.1 ad Android SDK](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

Per installare solo componenti specifici, fare clic una sola volta sulla casella accanto alla piattaforma. È possibile selezionare i singoli componenti necessari:

[![E[Esempio di aggiunta di alcuni componenti di Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

Si noti che il numero di componenti da installare viene visualizzato accanto al pulsante **Applica modifiche**. Dopo aver fatto clic sul pulsante **Applica modifiche** viene visualizzata la schermata **Accettazione della licenza**, come indicato in precedenza.
Selezionare **Accetto** se si accettano i termini e le condizioni. È possibile che questa finestra di dialogo sia visualizzata più di una volta se i componenti da installare sono più di uno. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine del download e dell'installazione (l'operazione può richiedere molti minuti, a seconda del numero di componenti da scaricare), i componenti aggiunti vengono contrassegnati da un segno di spunta ed elencati con lo stato **Installato**.

### <a name="respository-selection"></a>Selezione di repository

Per impostazione predefinita Android SDK Manager scarica i componenti e gli strumenti della piattaforma da un repository gestito da Microsoft. Se è necessario accedere a piattaforme e strumenti sperimentali alfa/beta non ancora disponibili nel repository di Microsoft, è possibile impostare SDK Manager in modo che usi il repository di Google. Per questa impostazione fare clic sull'icona a forma di ingranaggio nell'angolo inferiore destro e selezionare **Repository > Google (non supportato)**:

[![S[Selezione del repository di Google](android-sdk-images/win/11-google-repo-w157-sml.png)](android-sdk-images/win/11-google-repo-w157.png#lightbox)

Quando è selezionato il repository di Google è possibile che nella scheda **Piattaforme** siano visibili pacchetti non disponibili in precedenza. Ad esempio, nella schermata precedente **Android SDK Platform 28** è stato aggiunto con il passaggio al repository Google. Tenere presente che l'uso del repository di Google non è supportato e pertanto è sconsigliato per le operazioni di sviluppo comuni.

Per tornare al repository di piattaforme e strumenti supportato, fare clic su **Microsoft (scelta consigliata)**. L'elenco di pacchetti e strumenti viene ripristinato con la selezione predefinita.


# [<a name="visual-studio-for-mac"></a>Visual Studio per Mac](#tab/macos)

## <a name="requirements"></a>Requisiti

Per usare Xamarin Android SDK Manager sono necessari i componenti seguenti:

-   Visual Studio per Mac 7.5 (o versione successiva).

In Xamarin Android SDK Manager è anche necessario Java Development Kit, che viene installato automaticamente con Xamarin.Android. Sono disponibili numerose alternative di JDK:

-   Per impostazione predefinita Xamarin.Android usa [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), versione necessaria per sviluppare il livello API 24 o un livello superiore. JDK 8 supporta anche i livelli API precedenti al livello 24.

-   È possibile continuare a usare [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) per attività di sviluppo specifiche per il livello API 23 o un livello precedente.

-   Se si usa Visual Studio per Mac 7.7 o versioni successive, è possibile provare a usare la [distribuzione di Microsoft Mobile OpenJDK](openjdk.md) (attualmente in anteprima) invece di JDK 8.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.
 
## <a name="sdk-manager"></a>SDK Manager 

Per avviare SDK Manager in Visual Studio per Mac, selezionare **Strumenti -> SDK Manager**:
 
[![L[Percorso della voce di menu Android SDK Manager](android-sdk-images/mac/01-sdk-manager-menu-item-m75-sml.png)](android-sdk-images/mac/01-sdk-manager-menu-item-m75.png#lightbox)

**Android SDK Manager** si apre nel **finestra Preferenze**, che contiene tre schede **Piattaforme**, **Strumenti** e **Percorsi**:

[![S[Screenshot di Android SDK Manager aperto nella scheda Piattaforme](android-sdk-images/mac/02-sdk-manager-platforms-m75-sml.png)](android-sdk-images/mac/02-sdk-manager-platforms-m75.png#lightbox)

Le schede di Android SDK Manager sono descritte nelle sezioni seguenti.


### <a name="locations-tab"></a>Scheda Percorsi

Nella scheda **Percorsi** sono disponibili tre impostazioni per la configurazione di Android SDK, Android NDK e Java SDK (JDK). Questi percorsi devono essere configurati correttamente per l'esatto funzionamento delle schede **Piattaforme** e **Strumenti**.

All'avvio, SDK Manage determina automaticamente il percorso per ogni pacchetto installato e indica che è stato **Trovato** inserendo un'icona di spunta verde accanto al percorso:

[![S[Screenshot della scheda Percorsi](android-sdk-images/mac/03-locations-tab-m75-sml.png)](android-sdk-images/mac/03-locations-tab-m75.png#lightbox)

Fare clic sul pulsante **Ripristina valori predefiniti** perché SDK Manager esegua la ricerca di SDK, NDK e JDK in percorsi predefiniti. 

In genere, si usa la scheda **Percorsi** per modificare il percorso di Android SDK e/o Java JDK. Non è necessario installare NDK per sviluppare app Xamarin.Android. NDK viene usato solo quando è necessario sviluppare parti di app che usano linguaggi di codice nativo, ad esempio C e C++.

### <a name="tools-tab"></a>Scheda Strumenti

Nella scheda **Strumenti** viene visualizzato un elenco di _strumenti_ e _funzionalità aggiuntive_. Usare questa scheda per installare gli strumenti di Android SDK, gli strumenti della piattaforma e gli strumenti di compilazione.
È anche possibile installare l'emulatore Android, il debugger di basso livello, NDK, l'accelerazione HAXM e le librerie di Google Play.

Ad esempio, per scaricare il pacchetto relativo all'emulatore Android di Google, selezionare il segno di spunta accanto a **Emulatore Android** e fare clic sul pulsante **Applica modifiche**:

[![I[Installazione dell'emulatore Android dalla scheda Strumenti](android-sdk-images/mac/04-tools-tab-m75-sml.png)](android-sdk-images/mac/04-tools-tab-m75.png#lightbox)

È possibile che venga visualizzato il messaggio _Prima di installare il pacchetto seguente è necessario accettarne le condizioni di licenza_:

[![L[Schermata Accettazione della licenza](android-sdk-images/mac/05-license-acceptance-m75-sml.png)](android-sdk-images/mac/05-license-acceptance-m75.png#lightbox)

Selezionare **Accetto** se si accettano i termini e le condizioni. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine dell'installazione, nella scheda **Strumenti** saranno visualizzati gli strumenti e le funzionalità aggiuntive installati.


### <a name="platforms-tab"></a>Scheda Piattaforme

Nella scheda **Piattaforme** viene visualizzato un elenco delle versioni SDK della piattaforma insieme ad altre risorse (ad esempio, le immagini del sistema) per ogni piattaforma:

[![S[Schermata del riquadro Piattaforme](android-sdk-images/mac/06-platforms-tab-m75-sml.png)](android-sdk-images/mac/06-platforms-tab-m75.png#lightbox)

In questa schermata vengono elencati la versione Android (ad esempio **Android 8.1**), il nome in codice (**Oreo**), il livello API (ad esempio **27**) e le dimensioni dei componenti per la piattaforma (ad esempio **1 GB**). La scheda **Piattaforme** consente di installare componenti per il livello API Android previsto come destinazione. Per altre informazioni sulle versioni di Android e i livelli API, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android).

Quando tutti i componenti di una piattaforma sono installati, il nome della piattaforma viene affiancato da un segno di spunta. Se non tutti i componenti di una piattaforma sono installati, la casella per la piattaforma è compilata. Fare clic sulla casella **freccia** a sinistra della piattaforma per espandere una piattaforma e visualizzare i relativi componenti e i componenti installati.
Fare clic su **freccia giù** per comprimere l'elenco dei componenti di una piattaforma.

Per aggiungere un'altra piattaforma a SDK, selezionare la casella accanto alla piattaforma finché viene visualizzato il segno di spunta per installare tutti i relativi componenti, quindi fare clic su **Applica modifiche**:

[![E[Esempio di aggiunta di tutti i componenti di una piattaforma](android-sdk-images/mac/07-install-all-m75-sml.png)](android-sdk-images/mac/07-install-all-m75.png#lightbox)

Per installare solo alcuni componenti, fare clic una sola volta sulla casella accanto alla piattaforma. È possibile selezionare i singoli componenti necessari:

[![E[Esempio di aggiunta di alcuni componenti](android-sdk-images/mac/08-individual-components-m75-sml.png)](android-sdk-images/mac/08-individual-components-m75.png#lightbox)

Si noti che il numero di componenti da installare viene visualizzato accanto al pulsante **Applica modifiche**. Dopo aver fatto clic sul pulsante **Applica modifiche** viene visualizzata la schermata **Accettazione della licenza**, come indicato in precedenza.
Selezionare **Accetto** se si accettano i termini e le condizioni. È possibile che questa finestra di dialogo sia visualizzata più di una volta se i componenti da installare sono più di uno. L'indicatore di stato nella parte inferiore della finestra indica lo stato del download e dell'installazione. Al termine del download e dell'installazione (l'operazione può richiedere molti minuti, a seconda del numero di componenti da scaricare), i componenti aggiunti vengono contrassegnati da un segno di spunta ed elencati con lo stato **Installato**.

### <a name="respository-selection"></a>Selezione di repository

Per impostazione predefinita Android SDK Manager scarica i componenti e gli strumenti della piattaforma da un repository gestito da Microsoft. Se è necessario accedere a piattaforme e strumenti sperimentali alfa/beta non ancora disponibili nel repository di Microsoft, è possibile impostare SDK Manager in modo che usi il repository di Google. Per questa impostazione fare clic sull'icona a forma di ingranaggio nell'angolo inferiore destro e selezionare **Repository > Google (non supportato)**:

[![S[Selezione del repository di Google](android-sdk-images/mac/09-google-repo-m75-sml.png)](android-sdk-images/mac/09-google-repo-m75.png#lightbox)

Quando è selezionato il repository di Google è possibile che nella scheda **Piattaforme** siano visibili pacchetti non disponibili in precedenza. Ad esempio, nella schermata precedente **Android SDK Platform 28** è stato aggiunto con il passaggio al repository Google. Tenere presente che l'uso del repository di Google non è supportato e pertanto è sconsigliato per le operazioni di sviluppo comuni.

Per tornare al repository di piattaforme e strumenti supportato, fare clic su **Microsoft (scelta consigliata)**. L'elenco di pacchetti e strumenti viene ripristinato con la selezione predefinita.

-----

 
## <a name="summary"></a>Riepilogo

In questa guida è stato spiegato come installare e usare lo strumento Xamarin Android SDK Manager in Visual Studio e Visual Studio per Mac.


## <a name="related-links"></a>Collegamenti correlati

- [Understanding Android API Levels (Informazioni sui livelli API Android)](~/android/app-fundamentals/android-api-levels.md)
- [Modifiche agli strumenti di Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
