---
title: Installazione di Windows
description: Questa guida descrive le operazioni necessarie per l'installazione di Xamarin.Android per Visual Studio in Windows e illustra come configurare Xamarin.Android per compilare la prima applicazione Xamarin.Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 7cf21e75c9ae2f3c27b07cb20f1044779b42b06b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
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

Per informazioni dettagliate sull'installazione di Xamarin per l'uso con Visual Studio in Windows, vedere la guida per l'[installazione di Windows](~/cross-platform/get-started/installation/windows.md).


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

Se non si dispone di un dispositivo Android fisico da usare per il test, è possibile usare un emulatore Android per testare l'app. Per altre informazioni sull'emulatore Android di Google, vedere [Android SDK Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) (Emulatore di Android SDK).

L'emulatore Android di Google usa Intel HAXM (Hardware Accelerated Execution Manager), che può entrare in conflitto con le tecnologie di virtualizzazione usate da altri emulatori. Le tre tecnologie di virtualizzazione principali sono:

-   **Hyper-V** (usato da Visual Studio Emulator for Android e dall'emulatore di Windows Phone) 

-   **Virtual Box** (usato da Genymotion)

-   **Intel HAXM** (usato dall'emulatore Android SDK di Google) 

Poiché la CPU di un computer di sviluppo può supportare una sola tecnologia di virtualizzazione alla volta, è consigliabile averne solo una in uso in un computer di sviluppo.

<a name="device" />

### <a name="android-device"></a>Dispositivo Android

Se si dispone di un dispositivo Android fisico da usare per il test, è il momento di configurarlo per il processo di sviluppo. Vedere [Configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md) per configurare il dispositivo Android per lo sviluppo e quindi connetterlo al computer per l'esecuzione e il debug di applicazioni Xamarin.Android.


## <a name="create-an-application"></a>Creare un'applicazione

Dopo avere installato Xamarin.Android, è possibile avviare Visual Studio per creare un nuovo progetto. Fare clic su **File > Nuovo > Progetto** per iniziare a creare l'app:

![Come creare un nuovo progetto](windows-images/10-new-project.png)

Nella finestra di dialogo **Nuovo progetto** selezionare **Android** in **Modelli** e fare clic su **App vuota (Android)** nel riquadro di destra. Immettere un nome per l'app (nella schermata seguente l'app è chiamata **MyApp**) e quindi fare clic su **OK**:

[![Finestra di dialogo Nuovo progetto per la creazione di un'app Android vuota](windows-images/11-first-app-sml.png)](windows-images/11-first-app.png#lightbox)

La procedura è terminata. A questo punto si è pronti per usare Xamarin.Android per creare applicazioni Android.


## <a name="summary"></a>Riepilogo

In questo articolo si è appreso come configurare e installare la piattaforma Xamarin.Android in Windows, come configurare Visual Studio con percorsi di installazione personalizzati per Java JDK e Android SDK (facoltativo), come avviare SDK Manager per installare altri componenti Android SDK, come configurare un dispositivo Android o un emulatore e come iniziare a creare la prima applicazione.

Il passaggio successivo consiste nel seguire le esercitazioni [Hello, Android](~/android/get-started/hello-android/index.md) per imparare a creare un'app Xamarin.Android funzionante.


## <a name="related-links"></a>Collegamenti correlati

- [Download di Visual Studio](https://www.visualstudio.com/vs/)
- [Installazione degli strumenti di Visual Studio per Xamarin](~/cross-platform/get-started/installation/windows.md)
- [Requisiti di sistema](~/cross-platform/get-started/requirements.md)
- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Emulatore di Android SDK](~/android/get-started/installation/android-emulator/index.md)
- [Configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md)
