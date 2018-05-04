---
title: 'Installazione e configurazione di usura onXamarin.Android del sistema operativo '
description: In questo articolo vengono illustrati i passaggi di installazione e i dettagli di configurazione necessari per preparare il computer e dispositivi per lo sviluppo, usare Android. Alla fine di questo articolo, è necessario un lavoro di installazione di xamarin usura integrati in Visual Studio per Mac e/o Microsoft Visual Studio, e sarà possibile iniziare a creare la prima applicazione usura xamarin.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 14162663c518fdd1324f2b0340592fbae491d112
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="setup-and-installation"></a>Installazione e configurazione

_In questo articolo vengono illustrati i passaggi di installazione e i dettagli di configurazione necessari per preparare il computer e dispositivi per lo sviluppo, usare Android. Alla fine di questo articolo, è necessario un lavoro di installazione di xamarin usura integrati in Visual Studio per Mac e/o Microsoft Visual Studio, e sarà possibile iniziare a creare la prima applicazione usura xamarin._

## <a name="requirements"></a>Requisiti

Per creare applicazioni basate su Xamarin Android accenti è necessario quanto segue:

-   **Visual Studio o Visual Studio per Mac** &ndash; è se si utilizza Visual Studio, Visual Studio 2015 Professional o versione successiva è richiesto.

-   **Xamarin** &ndash; xamarin 4.17 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-   **Android SDK** -Android SDK 5.0.1 (API 21) o versioni successive deve essere installato mediante Android SDK Manager.

-   **Java Developer Kit** &ndash; lo sviluppo con Xamarin Android richiede [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lo sviluppo per il livello di API 24 o superiore (JDK 1.8 supporta inoltre API livelli precedenti a 24).

È possibile continuare a utilizzare [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) lo sviluppo in modo specifico per il livello API 23 o precedenti.

> [!IMPORTANT]
> Xamarin.Android non supporta JDK 9.

## <a name="installation"></a>Installazione

Dopo aver installato xamarin, eseguire la procedura seguente in modo che si è pronti per compilare e testare le app Android, usare: 

1.  Installare Android SDK e gli strumenti necessari.
2.  Configurare un dispositivo di test.
3.  Creare la prima app Android con accenti.

Questi passaggi sono descritti nelle sezioni seguenti.


### <a name="install-android-sdk-and-tools"></a>Installare Android SDK e strumenti 

Avviare il **Android SDK Manager**: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Come avviare Android SDK Manager in Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Come avviare Android SDK Manager in Visual Studio per Mac](installation-images/xs/sdk-menu.png)

-----


Verificare che il seguente Android SDK e gli strumenti installati:

* Android strumenti SDK v 24.0.0 o versioni successive e
* Android 4.4W (API20), o
* Android 5.0.1 (API21) o versione successiva.

Se non si dispone di SDK e installati gli strumenti più recenti, scaricare gli strumenti SDK richiesti *e* i bit di API (potrebbe essere necessario scorrere un bit per individuarli &ndash; seguito è riportata la selezione di API): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Schermata di esempio SDK Manager dell'abilitazione 5.0.1 Android componenti](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Schermata di esempio SDK Manager di abilitazione di Android 4.4 e 5.0.1 componenti](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configurazione

Prima di poter usare l'app di test, è necessario configurare un emulatore, usare Android o un dispositivo reale, usare Android. 


### <a name="android-wear-emulator"></a>Emulatore Android usura

Prima di poter utilizzare un emulatore, usare Android, è necessario configurare un Android accenti dispositivo virtuale Android (AVD) utilizzando il **Gestione emulatori di Google**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Come avviare Gestione emulatori di Android da Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Come avviare Gestione emulatori di Android da Visual Studio per Mac](installation-images/xs/emulator-menu.png)

-----

Per ulteriori informazioni sull'impostazione di un emulatore, usare Android, vedere [Debug Android accenti in un emulatore](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo Android usura

Se si dispone di un dispositivo Android, usare, ad esempio un Smartwatch accenti Android, è possibile eseguire il debug dell'app in questo dispositivo anziché utilizzare un emulatore. Per informazioni sullo sviluppo con un dispositivo usura, vedere [eseguire il Debug su un dispositivo con accenti](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Creare la prima App di Android usura

Seguire il [salve, usura](~/android/wear/get-started/hello-wear.md) istruzioni per la creazione della prima applicazione di espressioni di controllo.


## <a name="packaging-your-app"></a>Creazione del pacchetto dell'App

Applicazioni Android usura vengono sempre distribuite con un'app telefono Android complementare. 

Quando si aggiunge l'applicazione, usare Android come un riferimento all'applicazione di Android principale viene automaticamente considerato come un progetto Android usura e lo genererà tutte XML e i metadati necessari per l'utente. Inoltre, verificherà che pacchetto e numeri di versione deve corrispondere in modo da spedire facilmente le app da Google Play. 

Per ulteriori informazioni sulla creazione pacchetti di App usura, vedere [utilizzo di creazione del pacchetto](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Collegamenti correlati

- [SkeletonWear (esempio)](https://developer.xamarin.com/samples/SkeletonWear/)
