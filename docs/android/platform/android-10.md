---
title: Funzionalità di Android 10
description: Come iniziare a sviluppare app per Android 10 con Novell. Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: df9fa43d2071d273104edafbe6b880a97afb3f96
ms.sourcegitcommit: e354aabfb39598e0ce11115db3e6bcebb9f68338
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273147"
---
# <a name="android-10-with-xamarin"></a>Android 10 con Novell

_Come iniziare a sviluppare app per Android 10 con Novell. Android._

Android 10 è ora disponibile da Google. In questa versione sono state rese disponibili numerose nuove funzionalità e API e molte di esse sono necessarie per sfruttare le nuove funzionalità hardware nei dispositivi Android più recenti.

![Logo Android 10](~/android/platform/android-10-images/android10_black.png)

Questo articolo è strutturato per iniziare a sviluppare app Novell. Android per Android 10. Viene illustrato come installare gli aggiornamenti necessari, configurare l'SDK e preparare un emulatore o un dispositivo per il test. Viene inoltre fornita una descrizione delle nuove funzionalità di Android 10 e viene fornito un esempio di codice sorgente in cui viene illustrato come utilizzare alcune delle principali funzionalità di Android 10.

Novell. Android 10,0 fornisce supporto per Android 10. Per ulteriori informazioni sul supporto di Novell. Android per Android 10, vedere le [Note sulla versione di Novell. android 10,0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Requisiti

L'elenco seguente è necessario per usare le funzionalità di Android 10 nelle app basate su Novell:

- **Visual Studio** : visual studio 2019 è consigliato. In Windows Update per Visual Studio 2019 versione 16,3 o successiva. In macOS eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8,3 o successiva.
- **Novell. Android** : Novell. Android 10,0 o versione successiva deve essere installato con Visual Studio (Novell. Android viene installato automaticamente come parte del carico di lavoro **sviluppo di applicazioni per dispositivi mobili con .NET** in Windows e installato come parte di **Visual Studio per il programma di installazione di Mac**)
- **Java Developer Kit** : lo sviluppo di Novell. Android 10,0 richiede JDK 8. La distribuzione di Microsoft OpenJDK viene installata automaticamente come parte di Visual Studio.
- **Android SDK** Android SDK API 29 deve essere installata tramite il gestore Android SDK.

## <a name="get-started"></a>Attività iniziali

Per iniziare a sviluppare app Android 10 con Novell. Android, è necessario scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare il primo progetto Android 10:

1. **È consigliabile usare Visual Studio 2019**. Eseguire l'aggiornamento a Visual Studio 2019 versione 16,3 o successiva. Se si usa Visual Studio per Mac 2019, eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8,3 o successiva.
2. Installare gli strumenti e i pacchetti **Android 10 (API 29)** tramite SDK Manager.
    - Piattaforma SDK Android 10 (API 29)
    - Immagine di sistema Android 10 (API 29)
    - Android SDK build-Tools 29.0.0 +
    - Piattaforma Android SDK-strumenti 29.0.0 +
    - Emulatore Android 29.0.0 +
3. Creare un nuovo progetto Novell. Android destinato a Android 10,0.
4. Configurare un emulatore o un dispositivo per il test delle app Android 10.

Ognuno di questi passaggi è illustrato di seguito:

### <a name="update-visual-studio"></a>Aggiornare Visual Studio

Visual Studio 2019 è consigliato per la creazione di app Android 10 con Novell.

Se si usa Visual Studio 2019, eseguire l'aggiornamento a Visual Studio 2019 versione 16,3 o successiva. per istruzioni, vedere [aggiornare Visual studio 2019 alla versione più recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio). In macOS eseguire l'aggiornamento a Visual Studio 2019 per Mac 8,3 o versione successiva. per istruzioni, vedere [aggiornare Visual studio 2019 per Mac alla versione più recente](https://docs.microsoft.com/en-us/visualstudio/mac/update).

### <a name="install-the-android-sdk"></a>Installare il Android SDK

Per creare un progetto con Novell. Android 10,0, è necessario prima di tutto usare gestione Android SDK per installare la piattaforma SDK per **Android 10 (livello API 29)**.

1. Avviare SDK Manager. In Visual Studio fare clic su **strumenti > Android > Android SDK Manager.** In Visual Studio per Mac fare clic su **strumenti > SDK Manager.**
2. Nell'angolo in basso a destra fare clic sull'icona a forma di ingranaggio e selezionare **Repository > Google (non supportato):**

    ![Selezione repository di Android SDK Manager](~/android/platform/android-10-images/sdkrepository.png)

3. Installare i pacchetti della **piattaforma Android 10 SDK** , elencati come **Android SDK piattaforma 29** nella scheda **Platforms** (per ulteriori informazioni sull'utilizzo di SDK Manager, vedere [Android SDK installazione](https://docs.microsoft.com/en-us/xamarin/android/get-started/installation/android-sdk)):

    ![Scheda della piattaforma di Android SDK Manager](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Creare un progetto Novell. Android

Creare un nuovo progetto Novell. Android. Se non si ha familiarità con lo sviluppo di Android con Novell, vedere [Hello, Android](https://docs.microsoft.com/en-us/xamarin/android/get-started/hello-android/index) per altre informazioni sulla creazione di progetti Novell. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione per la destinazione Android 10,0 o versione successiva. Ad esempio, per fare riferimento al progetto per Android 10, è necessario configurare il livello API Android di destinazione del progetto su **android 10,0 (API 29)**. Sono incluse sia la **versione del Framework di destinazione** che la **Android SDK di destinazione** per l'API 29 o versioni successive. Per altre informazioni sulla configurazione dei livelli API Android, vedere [informazioni sui livelli di API Android.](https://docs.microsoft.com/en-us/xamarin/android/app-fundamentals/android-api-levels)

![Framework di destinazione Novell. Android](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configurare un dispositivo o un emulatore

Se si usa un dispositivo fisico, ad esempio un pixel, è possibile scaricare l'aggiornamento di Android 10 passando al `System` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 nelle impostazioni del telefono. Se si preferisce eseguire il flashing del dispositivo, vedere le istruzioni su come lampeggiare un' [immagine di fabbrica](https://developers.google.com/android/images) o un' [immagine OTA](https://developers.google.com/android/ota) nel dispositivo.

Se si usa un emulatore, creare un dispositivo virtuale per il livello API 29 e selezionare un'immagine basata su x86. Per informazioni sull'utilizzo del Android Device Manager per creare e gestire i dispositivi virtuali, vedere [gestione di dispositivi virtuali con il Android Device Manager.](https://docs.microsoft.com/en-us/xamarin/android/get-started/installation/android-emulator/device-manager) Per informazioni sull'utilizzo del emulatore Android per il test e il debug, vedere [debug nella emulatore Android.](https://docs.microsoft.com/en-us/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>Nuove funzionalità

Android 10 introduce un'ampia gamma di nuove funzionalità. Alcune di queste nuove funzionalità sono destinate a sfruttare le nuove funzionalità hardware offerte dai dispositivi Android più recenti, mentre altre sono progettate per migliorare ulteriormente l'esperienza utente di Android:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Migliora la tua app con le funzionalità e le API di Android 10

Quindi, quando si è pronti, approfondire Android 10 e ottenere informazioni sulle [nuove funzionalità e api](https://developer.android.com/preview/api-overview.html)  che che è possibile usare. Di seguito sono riportate alcune delle principali funzionalità per iniziare.

Queste funzionalità sono consigliate per ogni app:

- **Tema scuro:** Ensure un'esperienza coerente per gli utenti che abilitano il tema scuro a livello di sistema aggiungendo un [tema scuro](https://developer.android.com/preview/features/darktheme) o abilitando la [forza scura](https://developer.android.com/preview/features/darktheme#force_dark).

![Tema scuro](~/android/platform/android-10-images/darktheme.png)

- **Supporta la [navigazione gestuale](https://developer.android.com/preview/features/gesturalnav)** in l'app attraverso l'Edge-to-Edge e assicurandosi che i movimenti personalizzati siano complementari ai movimenti di navigazione del sistema.

![Spostamento movimenti](~/android/platform/android-10-images/gesturenavigation.png)

- **Ottimizza per i ripieghevoli:** Deliver le esperienze Edge-to-Edge sui dispositivi innovativi attuali [ottimizzando i ripieghevoli](https://developer.android.com/preview/features/foldables).

![Fold](~/android/platform/android-10-images/foldable.png)

Queste funzionalità sono consigliate se rilevanti per l'app:

- **Notifiche più interattive:** SE le notifiche includono messaggi, abilitare le [risposte e le azioni suggerite nelle notifiche](https://developer.android.com/preview/features#smart-suggestions) per coinvolgere gli utenti e consentire l'intervento immediato.
- **Biometria migliore:** SE si usa l'autenticazione biometrica, si passa a [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), il modo preferito per supportare l'autenticazione con impronta digitale sui dispositivi moderni.
- **Registrazione arricchita:** per supporta la didascalia o la registrazione del gioco, Abilita l' [acquisizione della riproduzione audio](https://developer.android.com/preview/features/playback-capture). È un ottimo modo per raggiungere un maggior numero di utenti e rendere più accessibile l'app.
- **Codec migliori:** app multimediali  per, provare [AV1](https://en.wikipedia.org/wiki/AV1) For video streaming e [HDR10 +](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) per High Dynamic Range video. Per lo streaming vocale e musicale, è possibile usare la codifica [Opus](http://opus-codec.org/) e per i musicisti, un' [API MIDI nativa](https://developer.android.com/preview/features/midi) is disponibile.
- **API di rete migliori:** SE l'app gestisce i dispositivi di Internet delle cose tramite Wi-Fi, provare le nuove [API di connessione di rete](https://developer.android.com/preview/features#peer2peer) for, ad esempio la configurazione, il download o la stampa.

Queste sono solo alcune delle numerose nuove funzionalità e API in Android 10. Per visualizzarli tutti, visitare il [sito Android 10 per sviluppatori](https://developer.android.com/about/versions/10/highlights).

## <a name="behavior-changes"></a>Modifiche funzionali

Quando la versione di Android di destinazione è impostata sul livello API 29, le modifiche apportate alla piattaforma influiscono sul comportamento dell'app anche se non si implementano le nuove funzionalità descritte in precedenza. Il seguente elenco è un breve riepilogo delle modifiche:

- [Per garantire la stabilità e la compatibilità delle app, la piattaforma Android limita ora le interfacce non SDK che l'app può usare in Android 10](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions).
- La [memoria condivisa è cambiata](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory).
- Il [runtime di Android & la correttezza AOT](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat).
- Le [autorizzazioni per gli intenti a schermo intero devono richiedere `USE_FULL_SCREEN_INTENT`](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Supporto per ripieghevoli](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto Android 10 e spiega come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Novell. Android con Android 10. Fornisce una panoramica delle funzionalità principali disponibili in Android 10. Sono inclusi collegamenti alla documentazione API e agli argomenti per sviluppatori Android che consentono di iniziare a creare app per Android 10. Sono state inoltre evidenziate le modifiche più importanti del comportamento di Android 10 che potrebbero influito sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Android 10](https://developer.android.com/about/versions/10)
