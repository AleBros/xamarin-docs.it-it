---
title: 'Installazione e configurazione di Wear onXamarin.Android del sistema operativo '
description: Questo articolo illustra i passaggi di installazione e i dettagli di configurazione necessari per preparare il computer e dispositivi per lo sviluppo di Android Wear. Al termine di questo articolo, è necessario un lavoro di installazione di xamarin. Android Wear integrata in Visual Studio per Mac e/o Microsoft Visual Studio, e si sarà pronti per iniziare a creare la prima applicazione xamarin. Android Wear.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 0bae98a204ba3478834894d6c093259a8b2139b2
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827727"
---
# <a name="setup-and-installation"></a>Installazione e configurazione

_Questo articolo illustra i passaggi di installazione e i dettagli di configurazione necessari per preparare il computer e dispositivi per lo sviluppo di Android Wear. Al termine di questo articolo, è necessario un lavoro di installazione di xamarin. Android Wear integrata in Visual Studio per Mac e/o Microsoft Visual Studio, e si sarà pronti per iniziare a creare la prima applicazione xamarin. Android Wear._

## <a name="requirements"></a>Requisiti

Per creare App basate su Xamarin Android Wear è necessario quanto segue:

-   **Visual Studio o Visual Studio per Mac** &ndash; Visual Studio Community 2017 o versione successiva è obbligatorio.

-   **Xamarin. Android** &ndash; xamarin. Android 4.17 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-   **Android SDK** -SDK Android 5.0.1 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

-   **Java Developer Kit** &ndash; lo sviluppo di Xamarin. Android richiede [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) se si è lo sviluppo per il livello API 24 o versioni successive (JDK 1.8 supporta anche i livelli API precedenti a 24).

È possibile continuare a usare [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se si è lo sviluppo in modo specifico per il livello API 23 o versioni precedenti.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

## <a name="installation"></a>Installazione

Dopo aver installato xamarin. Android, seguire questa procedura in modo che si è pronti per compilare e testare App Android Wear: 

1.  Installare Android SDK e strumenti necessari.
2.  Configurare un dispositivo di test.
3.  Creare la prima app Android Wear.

Questi passaggi sono descritti nelle sezioni seguenti.


### <a name="install-android-sdk-and-tools"></a>Installare Android SDK e strumenti 

Avviare il **Android SDK Manager**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Come avviare Android SDK Manager in Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Come avviare Android SDK Manager in Visual Studio per Mac](installation-images/xs/sdk-menu.png)

-----


Assicurarsi di avere il seguente Android SDK e gli strumenti installati:

* Android SDK Tools v 24.0.0 o versione successiva, e
* Android 4.4W (API20), o
* Android 5.0.1 (API21) o versione successiva.

Se non è il SDK e gli strumenti installati più recente, scaricare gli strumenti SDK necessari *e* i bit di API (potrebbe essere necessario scorrere un po' per trovarli &ndash; la selezione di API è illustrata di seguito): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Screenshot di esempio di SDK Manager dell'abilitazione di Android 5.0.1 componenti](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Screenshot di esempio di SDK Manager di abilitazione di Android 4.4 e 5.0.1 componenti](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configurazione

Prima di poter usare l'app di test, è necessario configurare un emulatore Android Wear o un dispositivo Android Wear reale. 


### <a name="android-wear-emulator"></a>Emulatore di Android Wear

Prima di poter usare un emulatore Android Wear, è necessario configurare un Android Wear Android Virtual Device (AVD) con i **Gestione emulatori Google**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Come avviare Gestione emulatori Android da Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Come avviare Gestione emulatori Android da Visual Studio per Mac](installation-images/xs/emulator-menu.png)

-----

Per altre informazioni sulla configurazione di un emulatore Android Wear, vedere [il Debug di Android Wear in un emulatore](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo Android Wear

Se si dispone di un dispositivo Android Wear, ad esempio un Smartwatch Android Wear, è possibile eseguire il debug dell'app su questo dispositivo invece di usare un emulatore. Per informazioni sullo sviluppo con un dispositivo Wear, vedere [eseguire il Debug in un dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Creare la prima App di Android Wear

Seguire le [salve, Wear](~/android/wear/get-started/hello-wear.md) istruzioni usate per creare la prima app watch.


## <a name="packaging-your-app"></a>Creazione del pacchetto App

Le applicazioni Android wear vengono sempre distribuite con un'app per telefono Android complementare. 

Quando si aggiunge l'applicazione Android Wear come un riferimento all'applicazione Android principale viene automaticamente considerato come un progetto Android Wear e genererà tutti XML e i metadati necessari per l'utente. Inoltre, lo strumento verifica che i numeri di pacchetto e versione corrispondano in modo che è possibile fornire con facilità le tue App in Google Play. 

Per altre informazioni sulla creazione di pacchetti App Wear, vedere [utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Collegamenti correlati

- [SkeletonWear (esempio)](https://developer.xamarin.com/samples/monodroid/wear/SkeletonWear/)
