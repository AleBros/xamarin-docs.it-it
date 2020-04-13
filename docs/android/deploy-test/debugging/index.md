---
title: Debug Xamarin.Android apps on Devices and Emulators
description: Come eseguire test e debug di un'app Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75556483"
---
# <a name="debug-xamarinandroid-apps"></a>Eseguire il debug di app Xamarin.AndroidDebug Xamarin.Android apps

Questa sezione illustra come eseguire il debug di un'app Xamarin.Android in dispositivi o emulatori.

## <a name="debugging-overview"></a>Cenni preliminari sul debug

Lo sviluppo di applicazioni Android richiede l'esecuzione dell'applicazione su hardware fisico o usando un emulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi può essere più semplice e più conveniente simulare/emulare hardware Android usando uno degli emulatori descritti di seguito.

### <a name="debugging-on-the-android-emulator"></a>[Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

Questo articolo illustra come avviare l'emulatore Android da Visual Studio ed eseguire l'app in un dispositivo virtuale.

### <a name="debugging-on-a-device"></a>[Debugging on a Device](~/android/deploy-test/debugging/debug-on-device.md) (Debug in un dispositivo)

Questo articolo illustra come configurare un dispositivo Android fisico in modo che l'applicazione Xamarin.Android possa essere distribuita direttamente da Visual Studio o Visual Studio per Mac.This article shows how to configure a physical Android device so that Xamarin.Android application can be deployed to it directly from either Visual Studio or Visual Studio for Mac.

### <a name="android-debug-log"></a>[Log di debug Android](~/android/deploy-test/debugging/android-debug-log.md)

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni è l'uso di `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che sarà probabilmente necessario usare durante la scrittura delle app. Questo è talvolta indicato come **logcat** a causa del comando digitato per recuperarlo. Questo articolo illustra la procedura per usare **logcat**.
