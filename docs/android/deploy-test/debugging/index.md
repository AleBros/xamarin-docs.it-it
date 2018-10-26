---
title: Debug di Xamarin.Android in dispositivi ed emulatori
description: Come eseguire test e debug di un'app Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8fb647e12de621fc0772ad5c18aac21e46758715
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113105"
---
# <a name="debugging"></a>Debug

Questa sezione illustra come eseguire il debug di un'app Xamarin.Android in dispositivi o emulatori.

## <a name="debugging-overview"></a>Cenni preliminari sul debug

Lo sviluppo di applicazioni Android richiede l'esecuzione dell'applicazione su hardware fisico o usando un emulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi può essere più semplice e più conveniente simulare/emulare hardware Android usando uno degli emulatori descritti di seguito.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

Questo articolo illustra come avviare l'emulatore Android da Visual Studio ed eseguire l'app in un dispositivo virtuale.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Debugging on a Device](~/android/deploy-test/debugging/debug-on-device.md) (Debug in un dispositivo)

L'articolo illustra come configurare un dispositivo Android fisico in modo che l'applicazione Xamarin.Android possa essere distribuita direttamente da Visual Studio o Visual Studio per Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log di debug Android](~/android/deploy-test/debugging/android-debug-log.md)

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni è l'uso di `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che sarà probabilmente necessario usare durante la scrittura delle app. Questo log viene a volte indicato come **logcat** a causa del comando digitato per recuperalo. Questo articolo illustra la procedura per usare **logcat**.

> [!WARNING]
> Si noti che **Xamarin Android Player** è stato deprecato. Per altre informazioni, vedere l'[annuncio nel post di questo blog](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/). Inoltre, l'**emulatore Android di Visual Studio** è stato deprecato a partire da Visual Studio 2017.
