---
title: Eseguire il debug di app Xamarin.Android in dispositivi ed emulatori
description: Come eseguire test e debug di un'app Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556483"
---
# <a name="debug-xamarinandroid-apps"></a>Eseguire il debug di app Xamarin.Android

Questa sezione illustra come eseguire il debug di un'app Xamarin.Android in dispositivi o emulatori.

## <a name="debugging-overview"></a>Cenni preliminari sul debug

Lo sviluppo di applicazioni Android richiede l'esecuzione dell'applicazione su hardware fisico o usando un emulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi può essere più semplice e più conveniente simulare/emulare hardware Android usando uno degli emulatori descritti di seguito.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

Questo articolo illustra come avviare l'emulatore Android da Visual Studio ed eseguire l'app in un dispositivo virtuale.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Debugging on a Device](~/android/deploy-test/debugging/debug-on-device.md) (Debug in un dispositivo)

Questo articolo illustra come configurare un dispositivo Android fisico in modo che l'applicazione Xamarin.Android possa essere distribuita direttamente da Visual Studio o Visual Studio per Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log di debug Android](~/android/deploy-test/debugging/android-debug-log.md)

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni è l'uso di `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che sarà probabilmente necessario usare durante la scrittura delle app. Questo log viene a volte indicato come **logcat** a causa del comando digitato per recuperalo. Questo articolo illustra la procedura per usare **logcat**.
