---
title: Debug di Xamarin.Android in dispositivi ed emulatori
description: Come eseguire test e debug di un'app Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: e1c2a591450d8a5fd0aebe2bceb1d914a711512e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732219"
---
# <a name="debugging"></a>Debug

Questa sezione illustra come eseguire il debug di un'app Xamarin.Android in dispositivi o emulatori.

## <a name="debugging-overview"></a>Cenni preliminari sul debug

Lo sviluppo di applicazioni Android richiede l'esecuzione dell'applicazione su hardware fisico o usando un emulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi può essere più semplice e più conveniente simulare/emulare hardware Android usando uno degli emulatori descritti di seguito.

### <a name="debugging-with-the-google-android-emulatorandroiddeploy-testdebuggingandroid-sdk-emulatorindexmd"></a>[Debug con l'emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

Questi articoli illustrano come usare l'emulatore predefinito incluso in Android SDK. L'emulatore è disponibile per Visual Studio per Windows e Visual Studio per Mac.

### <a name="visual-studio-android-emulatorandroiddeploy-testdebuggingvisual-studio-android-emulatormd"></a>[Emulatore di Visual Studio per Android](~/android/deploy-test/debugging/visual-studio-android-emulator.md)

L'articolo spiega come eseguire debug e test delle app Xamarin.Android usando l'emulatore Android predefinito di Visual Studio 2015. L'emulatore è una buona scelta se si usa Visual Studio 2015 e i profili di dispositivo personalizzati non occorrono.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Debugging on a Device](~/android/deploy-test/debugging/debug-on-device.md) (Debug in un dispositivo)

L'articolo illustra come configurare un dispositivo Android fisico in modo che l'applicazione Xamarin.Android possa essere distribuita direttamente da Visual Studio o Visual Studio per Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log di debug Android](~/android/deploy-test/debugging/android-debug-log.md)

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni è l'uso di `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che sarà probabilmente necessario usare durante la scrittura delle app. Questo log viene a volte indicato come **logcat** a causa del comando digitato per recuperalo. Questo articolo illustra la procedura per usare **logcat**.

> [!WARNING]
> Si noti che **Xamarin Android Player** è stato deprecato. Per altre informazioni, vedere l'[annuncio nel post di questo blog](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/).
