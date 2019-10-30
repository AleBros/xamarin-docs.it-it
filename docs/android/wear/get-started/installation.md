---
title: 'Installazione e configurazione del sistema operativo onXamarin. Android '
description: Questo articolo illustra i passaggi di installazione e i dettagli di configurazione necessari per preparare il computer e i dispositivi per lo sviluppo di Android Wear. Alla fine di questo articolo, si disporrà di un'installazione Novell. Android Wear integrata in Visual Studio per Mac e/o Microsoft Visual Studio e sarà possibile iniziare a creare la prima applicazione Novell. Android Wear.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 83ec214ae1838959355e99322ce5a809ead004fa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028734"
---
# <a name="setup-and-installation"></a>Installazione e configurazione

_Questo articolo illustra i passaggi di installazione e i dettagli di configurazione necessari per preparare il computer e i dispositivi per lo sviluppo di Android Wear. Alla fine di questo articolo, si disporrà di un'installazione Novell. Android Wear integrata in Visual Studio per Mac e/o Microsoft Visual Studio e sarà possibile iniziare a creare la prima applicazione Novell. Android Wear._

## <a name="requirements"></a>Requisiti

Per creare app di Android Wear basate su Novell, è necessario quanto segue:

- È necessario **Visual Studio o Visual Studio per Mac** &ndash; visual studio 2017 community o versione successiva.

- **Novell. android** &ndash; Novell. Android 4,17 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- **Android SDK** -Android SDK 5.0.1 (API 21) o versione successiva deve essere installato tramite il gestore di Android SDK.

- **Java Developer Kit** &ndash; lo sviluppo di Novell Android richiede [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) se si sta sviluppando per il livello API 24 o versione successiva (JDK 1,8 supporta anche i livelli API precedenti a 24).

È possibile continuare a usare [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si sta sviluppando in modo specifico per il livello API 23 o versioni precedenti.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

## <a name="installation"></a>Installazione

Dopo aver installato Novell. Android, seguire questa procedura in modo da essere pronti per compilare e testare le app di Android Wear: 

1. Installare gli strumenti e Android SDK necessari.
2. Configurare un dispositivo di test.
3. Creare la prima app Android Wear.

Questi passaggi sono descritti nelle sezioni seguenti.

### <a name="install-android-sdk-and-tools"></a>Installare Android SDK e strumenti 

Avviare **gestione Android SDK**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Come avviare Gestione Android SDK in Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Come avviare Gestione Android SDK in Visual Studio per Mac](installation-images/xs/sdk-menu.png)

-----

Verificare che siano installati i Android SDK e gli strumenti seguenti:

- Android SDK Tools v 24.0.0 o versione successiva e
- Android 4.4 W (API20) o
- Android 5.0.1 (API21) o versione successiva.

Se non sono installati gli SDK e gli strumenti più recenti, scaricare gli strumenti SDK richiesti *e* i bit per le API. potrebbe essere necessario scorrere un bit per trovarli &ndash; la selezione dell'API è riportata di seguito: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Schermata di esempio di SDK Manager dell'abilitazione dei componenti Android 5.0.1](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Schermata di esempio di SDK Manager dell'abilitazione dei componenti Android 4,4 e 5.0.1](installation-images/xs/sdk-select.png)

-----

## <a name="configuration"></a>Configurazione

Prima di poter usare il test dell'app, è necessario configurare un emulatore Android Wear o un dispositivo Android Wear effettivo. 

### <a name="android-wear-emulator"></a>Emulatore di Android Wear

Prima di poter usare un emulatore Android Wear, è necessario configurare un dispositivo Android Wear Android Virtual Device (AVD) usando **Gestione emulatori Google**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Come avviare Gestione emulatore Android da Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Come avviare Gestione emulatore Android da Visual Studio per Mac](installation-images/xs/emulator-menu.png)

-----

Per altre informazioni sulla configurazione di un emulatore Android Wear, vedere [eseguire il debug di Android Wear in un emulatore](~/android/wear/deploy-test/debug-on-emulator.md).

### <a name="android-wear-device"></a>Dispositivo Android Wear

Se si dispone di un dispositivo Android Wear, ad esempio un SmartWatch Android Wear, è possibile eseguire il debug dell'app su questo dispositivo anziché usare un emulatore. Per informazioni sullo sviluppo con un dispositivo Wear, vedere [eseguire il debug su un dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).

## <a name="create-your-first-android-wear-app"></a>Creare la prima app Android Wear

Seguire le istruzioni [Hello, Wear](~/android/wear/get-started/hello-wear.md) per compilare la prima app Watch.

## <a name="packaging-your-app"></a>Creazione del pacchetto dell'app

Le applicazioni Android Wear vengono sempre distribuite con un'app per telefoni Android complementare. 

Quando si aggiunge l'applicazione Android Wear come riferimento all'applicazione Android principale, si presuppone che sia un progetto Android Wear e generi automaticamente tutti i metadati e i XML necessari. Inoltre, verificherà che i numeri di pacchetto e di versione corrispondano, in modo da poter inviare facilmente le app a Google Play. 

Per altre informazioni sulla creazione di pacchetti di app Wear, vedere uso della creazione di [pacchetti](~/android/wear/deploy-test/packaging.md).

## <a name="related-links"></a>Collegamenti correlati

- [SkeletonWear (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)
