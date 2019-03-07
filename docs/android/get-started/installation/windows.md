---
title: Installazione di Windows
description: Questa guida descrive le operazioni necessarie per l'installazione di Xamarin.Android per Visual Studio in Windows e illustra come configurare Xamarin.Android per compilare la prima applicazione Xamarin.Android.
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 26865fdbfc1cc6f11a978a70bd768e63771bcd4b
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197472"
---
# <a name="windows-installation"></a>Installazione di Windows

_Questa guida descrive le operazioni necessarie per l'installazione di Xamarin.Android per Visual Studio in Windows e illustra come configurare Xamarin.Android per compilare la prima applicazione Xamarin.Android._


## <a name="overview"></a>Panoramica

Poiché Xamarin è ora incluso gratuitamente in tutte le edizioni di Visual Studio e non richiede una licenza separata, è possibile usare il programma di installazione di Visual Studio per scaricare e installare gli strumenti di Xamarin.Android.
L'installazione manuale e le operazioni per le licenze richieste per le versioni precedenti di Xamarin.Android non sono più necessarie. In questa guida si imparerà a:

-   Configurare percorsi personalizzati per Java Development Kit, Android SDK e Android NDK.

-   Avviare Android SDK Manager per scaricare e installare altri componenti di Android SDK.

-   Preparare un dispositivo Android o un emulatore per il debug e il test.

-   Creare il primo progetto di app Xamarin.Android.

Al completamento di questa guida, sarà stata messa in funzione un'installazione Xamarin.Android integrata in Visual Studio e sarà possibile iniziare a compilare la prima applicazione Xamarin.Android.

## <a name="installation"></a>Installazione

Per informazioni dettagliate sull'installazione di Xamarin per l'uso con Visual Studio in Windows, vedere la guida per l'[installazione di Windows](~/get-started/installation/windows.md).


## <a name="configuration"></a>Configurazione

Xamarin.Android usa Java Development Kit (JDK) e Android SDK per compilare le app. Durante l'installazione, il programma di installazione di Visual Studio inserisce questi strumenti nei percorsi predefiniti e configura l'ambiente di sviluppo con la configurazione del percorso appropriata. È possibile visualizzare e modificare questi percorsi facendo clic su **Strumenti > Opzioni > Xamarin > Impostazioni Android**:

![Finestra di dialogo Impostazioni Android di Xamarin](windows-images/07-settings.png)

La maggior parte degli utenti potrà usare questi percorsi predefiniti senza apportare ulteriori modifiche. Tuttavia, potrebbe essere necessario configurare Visual Studio con percorsi personalizzati per questi strumenti (ad esempio, se Java JDK, Android SDK o NDK è stato installato in un percorso diverso). Fare clic su **Modifica** accanto al percorso che si vuole modificare e quindi selezionare il nuovo percorso.

Xamarin.Android usa [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), la versione necessaria per le attività di sviluppo per il livello API 24 o un livello superiore (JDK 8 supporta anche i livelli API precedenti al livello 24). È possibile continuare a usare [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) per attività di sviluppo specifiche per il livello API 23 o un livello precedente.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.


### <a name="android-sdk-manager"></a>Android SDK Manager

Android usa più impostazioni dei livelli API di Android per determinare la compatibilità dell'app tra le diverse versioni di Android. Per altre informazioni sui livelli API di Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API di Android).
A seconda del livello API di Android di destinazione, potrebbe essere necessario scaricare e installare componenti aggiuntivi di Android SDK. Inoltre, potrebbe essere necessario installare gli strumenti facoltativi e le immagini dell'emulatore disponibili in Android SDK. A tale scopo, usare **Android SDK Manager**. È possibile avviare **Android SDK Manager** facendo clic su **Strumenti > Android > Android SDK Manager**:

[![Come avviare Android SDK Manager](windows-images/08-sdk-manager-sml.png)](windows-images/08-sdk-manager.png#lightbox)

Per impostazione predefinita, Visual Studio installa Google Android SDK Manager:

![Schermata di esempio di Google Android SDK Manager](windows-images/09-google-sdk-manager.png)

È possibile usare Google Android SDK Manager per installare le versioni del pacchetto di Android SDK Tools fino alla versione 25.2.3. Tuttavia, se occorre usare una versione successiva del pacchetto di Android SDK Tools, è necessario installare il plug-in Xamarin Android SDK Manager per Visual Studio (disponibile in Visual Studio Marketplace). Ciò è necessario perché la versione autonoma di Google SDK Manager è stata deprecata nella versione 25.2.3 del pacchetto Android SDK Tools. 

Per altre informazioni sull'uso di Xamarin Android SDK Manager, vedere [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md).

### <a name="android-emulator"></a>Emulatore Android

L'[emulatore Android](https://developer.android.com/studio/run/emulator) può essere utile per sviluppare e testare un'app Xamarin.Android. È ad esempio possibile che un dispositivo fisico, ad esempio un tablet, non sia immediatamente disponibile durante lo sviluppo, o che uno sviluppatore voglia eseguire alcuni test di integrazione sul computer prima di eseguire il commit del codice.

L'emulazione di un dispositivo Android in un computer richiede i componenti seguenti:

* **Emulatore Android di Google**: si tratta di un emulatore basato su [QEMU](https://www.qemu.org/) che crea un dispositivo virtualizzato eseguito nella workstation dello sviluppatore.
* **Immagine dell'emulatore**: un'_immagine dell'emulatore_ è un modello o una specifica dell'hardware e del sistema operativo che deve essere virtualizzato. Ad esempio, un'immagine dell'emulatore identificherà i requisiti hardware per Nexus 5X che esegue Android 7.0 con Google Play Services installato. Un'altra immagine dell'emulatore potrebbe specificare una tablet da 10" che esegue Android 6.0.
* **Dispositivo virtuale Android (AVD)** un _dispositivo virtuale Android_ è un dispositivo Android emulato creato da un'immagine dell'emulatore. Durante l'esecuzione e il test di app Android, Xamarin.Android avvierà l'emulatore Android, avviano un AVD specifico, installerà il pacchetto APK ed eseguirà l'app.

Grazie a immagini dell'emulatore speciali ottimizzate per l'architettura x86 e a una delle due tecnologie di virtualizzazione, è possibile ottenere un miglioramento significativo delle prestazioni durante lo sviluppo di computer x86:

1. Microsoft Hyper-V&ndash;: disponibile in computer che eseguono l'aggiornamento di aprile 2018 di Windows 10 o versioni successive.
2. Hardware Accelerated Execution Manager (HAXM) &ndash; di Intel: disponibile in computer x86 che eseguono OS X, macOS o versione precedente di Windows.

Per altre informazioni sull'emulatore Android, Hyper-V e HAXM, vedere la guida [Accelerazione hardware per le prestazioni dell'emulatore](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

> [!NOTE]
> Nelle versioni di Windows precedenti all'aggiornamento di aprile 2018 di Windows 10, HAXM non è compatibile con Hyper-V. In questo scenario è necessario [disabilitare Hyper-V](~/android/get-started/installation/android-emulator/troubleshooting.md#disable-hyperv) oppure usare le immagini dell'emulatore più lente non ottimizzate per x86.


<a name="device" />

### <a name="android-device"></a>Dispositivo Android

Se si dispone di un dispositivo Android fisico da usare per il test, è il momento di configurarlo per il processo di sviluppo. Vedere [Configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md) per configurare il dispositivo Android per lo sviluppo e quindi connetterlo al computer per l'esecuzione e il debug di applicazioni Xamarin.Android.


## <a name="create-an-application"></a>Creare un'applicazione

Dopo avere installato Xamarin.Android, è possibile avviare Visual Studio per creare un nuovo progetto. Fare clic su **File > Nuovo > Progetto** per iniziare a creare l'app:

![Come creare un nuovo progetto](windows-images/10-new-project.png)

Nella finestra di dialogo **Nuovo progetto** selezionare **Android** in **Modelli** e fare clic su **App Android** nel riquadro destro. Immettere un nome per l'app (nella schermata seguente l'app è chiamata **MyApp**) e quindi fare clic su **OK**:

[![Finestra di dialogo Nuovo progetto per la creazione di un'app Android vuota](windows-images/11-first-app-sml.w157.png)](windows-images/11-first-app.w157.png#lightbox)

La procedura è terminata. A questo punto si è pronti per usare Xamarin.Android per creare applicazioni Android.


## <a name="summary"></a>Riepilogo

In questo articolo si è appreso come configurare e installare la piattaforma Xamarin.Android in Windows, come configurare Visual Studio con percorsi di installazione personalizzati per Java JDK e Android SDK (facoltativo), come avviare SDK Manager per installare altri componenti Android SDK, come configurare un dispositivo Android o un emulatore e come iniziare a creare la prima applicazione.

Il passaggio successivo consiste nel seguire le esercitazioni [Hello, Android](~/android/get-started/hello-android/index.md) per imparare a creare un'app Xamarin.Android funzionante.


## <a name="related-links"></a>Collegamenti correlati

- [Download di Visual Studio](https://visualstudio.microsoft.com/vs/)
- [Installazione degli strumenti di Visual Studio per Xamarin](~/get-started/installation/windows.md)
- [Requisiti di sistema](~/cross-platform/get-started/requirements.md)
- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Configurazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md)
- [Configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md)
- [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator#Requirements) (Eseguire app nell'emulatore Android)
