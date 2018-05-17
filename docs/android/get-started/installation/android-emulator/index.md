---
title: Configurazione dell'emulatore Android
description: Questa sezione illustra come preparare l'emulatore Android di Google per testare l'app. Viene illustrato come accelerare l'emulatore per ottenere le massime prestazioni e come usare Gestione emulatori per creare e personalizzare dispositivi virtuali.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/25/2018
ms.openlocfilehash: 215e298068b7a3a23b2e469e923f172c8303bbcb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="android-emulator-setup"></a>Configurazione dell'emulatore Android

_Questa sezione illustra come preparare l'emulatore Android di Google per testare l'app. Viene illustrato come accelerare l'emulatore per ottenere le massime prestazioni e come usare Gestione emulatori per creare e personalizzare dispositivi virtuali._


## <a name="overview"></a>Panoramica

È possibile eseguire l'emulatore Android SDK di Google in un'ampia gamma di configurazioni per simulare i diversi dispositivi. Ognuna di queste configurazioni viene creata come _dispositivo virtuale_. In questa guida si apprenderà come accelerare l'emulatore Android per ottenere prestazioni migliori e come usare Gestione emulatori Android di Xamarin o la funzionalità legacy Gestione emulatori Google per creare dispositivi virtuali.


> [!NOTE]
> A partire da Android SDK Tools **26.0.1** e versioni successive, Google ha rimosso il supporto per le funzionalità AVD/SDK Manager esistenti a favore dell'uso dei nuovi strumenti dell'interfaccia della riga di comando. A causa di queste funzionalità deprecate vengono ora usati SDK Manager/Gestione dispositivi di Xamarin anziché SDK Manager/Gestione emulatori di Google per Android Tools 26.0.1 e versioni successive. Per altre informazioni su Xamarin SDK Manager, vedere [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md).


## <a name="sections"></a>Sezioni

### <a name="hardware-accelerationandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Accelerazione hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Come preparare il computer per ottenere prestazioni ottimali dall'emulatore Android di Google. Poiché l'emulatore Android di Google può risultare estremamente lento senza l'accelerazione hardware, è consigliabile abilitare l'accelerazione hardware nel computer prima di iniziare a usare l'emulatore.

### <a name="xamarin-android-device-managerandroidget-startedinstallationandroid-emulatorxamarin-device-managermd"></a>[Gestione dispositivi di Xamarin Android](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)

Come usare Xamarin Android Device Manager per creare e personalizzare dispositivi virtuali per l'emulatore Android di Google. **Gestione dispositivi di Xamarin Android**, attualmente in anteprima, è destinata a sostituire la funzionalità legacy Gestione emulatori Google. Se la destinazione è Android Oreo 8.0 o versione successiva, sarà necessario usare Gestione dispositivi di Xamarin Android anziché Gestione emulatori Google.

### <a name="google-emulator-managerandroidget-startedinstallationandroid-emulatorgoogle-emulator-managermd"></a>[Gestione emulatori Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md)

Come usare la funzionalità legacy Gestione emulatori Google per creare e personalizzare dispositivi virtuali per l'emulatore Android di Google. È possibile continuare a usare l'emulatore Android di Google con la versione originale di Gestione emulatori Google mantenendo Android SDK Tools versione 25.2.5 o precedente.

Dopo aver configurato l'emulatore Android SDK, vedere [Emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md) per informazioni su come avviarlo e usarlo per le attività di test e debug dell'app.
