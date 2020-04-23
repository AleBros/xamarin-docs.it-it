---
title: Android 10 caratteristiche
description: Come iniziare a sviluppare app per Android 10 usando Xamarin.Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: b26d99c131e9759da3077a767476de38712bcb18
ms.sourcegitcommit: a9280318bf7bb69e4e5744ee739e76a9cba36b28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82047573"
---
# <a name="android-10-with-xamarin"></a>Android 10 con Xamarin

_Come iniziare a sviluppare app per Android 10 usando Xamarin.Android._

Android 10 è ora disponibile da Google. Un certo numero di nuove funzionalità e API sono stati resi disponibili in questa versione, e molti di loro sono necessari per sfruttare le nuove funzionalità hardware nei dispositivi Android più recenti.

![Android 10 Logo](~/android/platform/android-10-images/android10_black.png)

Questo articolo è strutturato per aiutarti a iniziare a sviluppare app Xamarin.Android per Android 10. Viene illustrato come installare gli aggiornamenti necessari, configurare l'SDK e preparare un emulatore o un dispositivo per il test. Fornisce inoltre una struttura delle nuove funzionalità di Android 10 e fornisce codice sorgente di esempio che illustra come usare alcune delle funzionalità principali di Android 10.

Xamarin.Android 10.0 fornisce il supporto per Android 10. Per ulteriori informazioni sul supporto Xamarin.Android per Android 10, vedere le note sulla versione di [Xamarin.Android 10.0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Requisiti

L'elenco seguente è necessario per utilizzare le funzionalità di Android 10 nelle app basate su Xamarin:

- **Visual Studio** - Visual Studio 2019 è consigliato. In Windows Update a Visual Studio 2019 versione 16.3 o successiva. In macOS, eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8.3 o successiva.
- **Xamarin.Android** - Xamarin.Android 10.0 o versione successiva deve essere installato con Visual Studio (Xamarin.Android viene installato automaticamente come parte del carico di lavoro **di sviluppo per dispositivi mobili con .NET** in Windows e installato come parte del programma di installazione di Visual Studio per **Mac**)
- **Java Developer Kit** - Xamarin.Android 10.0 sviluppo richiede JDK 8. La distribuzione di Microsoft di OpenJDK viene installata automaticamente come parte di Visual Studio.
- **Android SDK:** Android SDK API 29 deve essere installato tramite Android SDK Manager.

## <a name="get-started"></a>Introduzione

Per iniziare a sviluppare app Android 10 con Xamarin.Android, devi scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare il tuo primo progetto Android 10:

1. **Visual Studio 2019 è consigliato**. Aggiornamento a Visual Studio 2019 versione 16.3 o successiva. Se si usa Visual Studio per Mac 2019, eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8.3 o successiva.
2. Installare i pacchetti e gli strumenti **di Android 10 (API 29)** tramite Gestione SDK.
    - Piattaforma SDK Android 10 (API 29)
    - Immagine del sistema Android 10 (API 29)
    - Strumenti di compilazione di Android SDK 29.0.0
    - Android SDK Platform-Tools 29.0.0
    - Emulatore Android 29.0.0
3. Creare un nuovo progetto Xamarin.Android destinato ad Android 10.0.Create a new Xamarin.Android project that targets Android 10.0.
4. Configurare un emulatore o un dispositivo per testare le app Android 10.Configure an emulator or device for testing Android 10 apps.

Ognuno di questi passaggi è spiegato di seguito:

### <a name="update-visual-studio"></a>Aggiornare Visual Studio

Visual Studio 2019 is recommended for building Android 10 apps using Xamarin.

Se si utilizza Visual Studio 2019, eseguire l'aggiornamento a Visual Studio 2019 versione 16.3 o successiva (per istruzioni, vedere [Aggiornare Visual Studio 2019 alla versione più recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). In macOS, eseguire l'aggiornamento a Visual Studio 2019 per Mac 8.3 o versione successiva (per istruzioni, vedere [Aggiornare Visual Studio 2019 per Mac alla versione più recente).](https://docs.microsoft.com/visualstudio/mac/update)

### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con Xamarin.Android 10.0, è innanzitutto necessario utilizzare Android SDK Manager per installare la piattaforma SDK per **Android 10 (livello API 29)**.

1. Avviare Gestione SDK. In Visual Studio, fare clic su **Strumenti > Android > Android SDK Manager.** In Visual Studio per Mac fare clic su **Strumenti > Gestione SDK.**
2. Nell'angolo in basso a destra, fai clic sull'icona a forma di ingranaggio e seleziona **Repository > Google (non supportato):**

    ![Selezione repository di Android SDK Manager](~/android/platform/android-10-images/sdkrepository.png)

3. Installare i pacchetti di **piattaforma SDK di Android 10,** elencati come Android SDK Platform **29** nella scheda **Piattaforme** (per ulteriori informazioni sull'utilizzo di Gestione SDK, vedere Configurazione di [Android SDK](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)):

    ![Scheda Piattaforma di gestione SDK Android](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Creare un progetto Xamarin.Android

Creare un nuovo progetto Xamarin.Android.Create a new Xamarin.Android project. Se non si ha familiarità con lo sviluppo Android con Xamarin, vedere Hello, Android per informazioni sulla creazione di progetti Xamarin.Android.If you are new to Android development with Xamarin, see [Hello, Android](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index) to learn about creating Xamarin.Android projects.

Quando crei un progetto Android, devi configurare le impostazioni della versione per Android 10.0 o versioni successive. Ad esempio, per impostare come destinazione il progetto per Android 10, è necessario configurare il livello API Android di destinazione del progetto su **Android 10.0 (API 29).** Sono incluse sia la **versione del framework** di destinazione che la versione di Android SDK di **destinazione** per l'API 29 o versione successiva. Per altre informazioni sulla configurazione dei livelli api Android, vedere Informazioni sui livelli di API Android.For more information about configuring Android API levels, see [Understanding Android API Levels.](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)

![Framework di destinazione Xamarin.AndroidXamarin.Android Target Framework](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configurare un dispositivo o un emulatore

Se si utilizza un dispositivo fisico come un Pixel, è possibile scaricare l'aggiornamento Android 10 accedendo al sistema > aggiornamento del sistema > verificare l'aggiornamento nelle impostazioni del telefono. Se preferisci lampeggiare il dispositivo, consulta le istruzioni su come lampeggiare [un'immagine](https://developers.google.com/android/images) di fabbrica o [un'immagine OTA](https://developers.google.com/android/ota) sul dispositivo.

Se si usa un emulatore, creare un dispositivo virtuale per il livello API 29 e selezionare un'immagine basata su x86. Per informazioni sull'utilizzo di Gestione dispositivi Android per creare e gestire dispositivi virtuali, vedere [Gestione di dispositivi virtuali con Gestione dispositivi Android.For](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager) information about using the Android Device Manager to create and manage virtual devices, see Managing Virtual Devices with the Android Device Manager. Per informazioni sull'uso dell'emulatore Android per il test e il debug, vedere [Debug nell'emulatore Android.For](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator) information about using the Android Emulator for testing and debugging, see Debugging on the Android Emulator.

## <a name="new-features"></a>Nuove funzionalità

Android 10 introduce una varietà di nuove funzionalità. Alcune di queste nuove funzionalità hanno lo scopo di sfruttare le nuove funzionalità hardware offerte dai più recenti dispositivi Android, mentre altre sono progettate per migliorare ulteriormente l'esperienza utente Android:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Migliora la tua app con le funzionalità e le API di Android 10

Successivamente, quando sei pronto, immerfai in Android 10 e scopri le [nuove funzionalità e LE API](https://developer.android.com/preview/api-overview.html) che puoi usare. Ecco alcune delle caratteristiche principali per iniziare.

Queste funzionalità sono consigliate per ogni app:

- **Tema scuro:** assicurati un'esperienza coerente per gli utenti che abilitano un tema scuro a livello di sistema aggiungendo un [tema](https://developer.android.com/preview/features/darktheme) scuro o [abilitando Force Dark](https://developer.android.com/preview/features/darktheme#force_dark).

![Tema scuro](~/android/platform/android-10-images/darktheme.png)

- **Supporta la [navigazione](https://developer.android.com/preview/features/gesturalnav)** gestuale nella tua app passando da bordo a bordo e assicurandoti che i gesti personalizzati siano complementari ai movimenti di navigazione del sistema.

![Navigazione gesture](~/android/platform/android-10-images/gesturenavigation.png)

- **Ottimizza per i pieghevoli:** offri esperienze senza soluzione di continuità e edge-to-edge sugli innovativi dispositivi di oggi [ottimizzando gli straordinari.](https://developer.android.com/preview/features/foldables)

![Pieghevole](~/android/platform/android-10-images/foldable.png)

Queste funzionalità sono consigliate se pertinenti per la tua app:

- **Notifiche più interattive:** se le notifiche includono messaggi, abilita [le risposte e le azioni suggerite nelle notifiche](https://developer.android.com/preview/features#smart-suggestions) per coinvolgere gli utenti e consentire loro di intervenire immediatamente.
- **Migliore biometria:** Se si utilizza l'autenticazione biometrica, passare a [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), il modo preferito per supportare l'autenticazione delle impronte digitali sui dispositivi moderni.
- **Registrazione arricchita:** per supportare i sottotitoli o la registrazione di gioco, abilitare l'acquisizione della [riproduzione audio.](https://developer.android.com/preview/features/playback-capture) È un ottimo modo per raggiungere più utenti e rendere la tua app più accessibile.
- **Codec migliori: per** le app multimediali, prova [AV1](https://en.wikipedia.org/wiki/AV1) per lo streaming video e [HDR10](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) per i video ad alta gamma dinamica. Per lo streaming vocale e musicale, è possibile utilizzare la codifica [Opus](http://opus-codec.org/) e, per i musicisti, è disponibile  [un'API MIDI nativa.](https://developer.android.com/preview/features/midi)
- **API** di rete migliori: se l'app gestisce i dispositivi IoT tramite Wi-Fi, prova le nuove [API](https://developer.android.com/preview/features#peer2peer) di connessione di rete per funzioni come la configurazione, il download o la stampa.

Queste sono solo alcune delle molte nuove funzionalità e API in Android 10. Per vederli tutti, visita il [sito Android 10 per gli sviluppatori.](https://developer.android.com/about/versions/10/highlights)

## <a name="behavior-changes"></a>Modifiche funzionali

Quando la versione di Android di destinazione è impostata sul livello API 29, sono disponibili diverse modifiche alla piattaforma che influiscono sul comportamento dell'app anche se non si implementano le nuove funzionalità descritte in precedenza. L'elenco seguente è un breve riepilogo di queste modifiche:

- [Per garantire la stabilità e la compatibilità dell'app, la piattaforma Android ora limita le interfacce non SDK che l'app può usare in Android 10.](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions)
- [La memoria condivisa è cambiata.](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory)
- [Android runtime & correttezza AOT](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat).
- [Le autorizzazioni per le `USE_FULL_SCREEN_INTENT`finalità a schermo intero devono richiedere ](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Supporto per pieghevoli](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Summary

Questo articolo ha introdotto Android 10 e ha spiegato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Xamarin.Android con Android 10. Ha fornito una panoramica delle funzionalità chiave disponibili in Android 10. Sono inclusi collegamenti alla documentazione dell'API e agli argomenti di Android Developer per aiutarti a iniziare a creare app per Android 10. Ha anche evidenziato le più importanti modifiche di comportamento di Android 10 che potrebbero influire sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Android 10](https://developer.android.com/about/versions/10)
