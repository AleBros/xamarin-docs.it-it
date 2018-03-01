---
title: Debug
description: Come eseguire test e debug di un'app Xamarin.Android
ms.topic: article
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: feb35c041349f3ce78490c8a2fc6a829f9d84a6d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="debugging"></a>Debug

## <a name="debuggin-overview"></a>Panoramica del debug

Lo sviluppo di applicazioni Android richiede l'esecuzione dell'applicazione su hardware fisico o su un emulatore o simulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi può essere più semplice e più conveniente simulare/emulare hardware Android usando uno degli emulatori descritti di seguito.


### <a name="android-sdk-emulatorandroiddeploy-testdebuggingandroid-sdk-emulatorindexmd"></a>[Android SDK Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) (Emulatore di Android SDK)

Questi articoli illustrano come usare l'emulatore predefinito incluso in Android SDK. L'emulatore è disponibile per Visual Studio per Windows e Visual Studio per Mac.

### <a name="visual-studio-android-emulatorandroiddeploy-testdebuggingvisual-studio-android-emulatormd"></a>[Emulatore di Visual Studio per Android](~/android/deploy-test/debugging/visual-studio-android-emulator.md)

L'articolo spiega come eseguire debug e test delle app Xamarin.Android usando l'emulatore Android predefinito di Visual Studio 2015. L'emulatore è una buona scelta se si usa Visual Studio 2015 e i profili di dispositivo personalizzati non occorrono.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Debugging on a Device](~/android/deploy-test/debugging/debug-on-device.md) (Debug in un dispositivo)

L'articolo illustra come configurare un dispositivo Android fisico in modo che l'applicazione Xamarin.Android possa essere distribuita direttamente da Visual Studio o Visual Studio per Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log di debug Android](~/android/deploy-test/debugging/android-debug-log.md)

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni è l'uso di `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che sarà probabilmente necessario usare durante la scrittura delle app. Questo log viene a volte indicato come **logcat** a causa del comando digitato per recuperalo. Questo articolo illustra la procedura per usare **logcat**.

> [!WARNING]
> Si noti che **Xamarin Android Player** è stato deprecato. Per altre informazioni, vedere l'[annuncio nel post di questo blog](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/).
