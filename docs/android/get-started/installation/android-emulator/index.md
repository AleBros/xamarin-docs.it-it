---
title: Configurazione dell'emulatore Android di Google
description: È possibile eseguire l'emulatore Android di Google in un'ampia gamma di configurazioni per simulare i diversi dispositivi. Questa guida spiega come preparare l'emulatore Android per testare l'app.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: e5ba2cc23ea9751ca60644d3eb5b7e3f31bbb6bb
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732531"
---
# <a name="google-android-emulator-setup"></a>Configurazione dell'emulatore Android di Google

_Questa guida spiega come preparare l'emulatore Android di Google per testare l'app._


## <a name="overview"></a>Panoramica

È possibile eseguire l'emulatore Android di Google in un'ampia gamma di configurazioni per simulare i diversi dispositivi. Ogni configurazione è chiamata _dispositivo virtuale_. Quando si distribuisce e si testa l'app nell'emulatore, si seleziona un dispositivo virtuale preconfigurato o personalizzato che simula un dispositivo Android fisico, come un telefono Pixel o Nexus.

Le sezioni elencate di seguito descrivono come accelerare l'emulatore Android di Google per ottenere prestazioni ottimali, come usare Android Device Manager per creare e personalizzare i dispositivi virtuali e come personalizzare le proprietà del profilo di un dispositivo virtuale. Inoltre, una sezione sulla risoluzione dei problemi illustra i problemi di configurazione più comuni e le relative soluzioni.

## <a name="sections"></a>Sezioni

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Accelerazione hardware per le prestazioni dell'emulatore](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Come preparare il computer per ottenere prestazioni ottimali dall'emulatore Android.
Poiché l'emulatore Android di Google può risultare estremamente lento senza l'accelerazione hardware, è consigliabile abilitare l'accelerazione hardware nel computer prima di iniziare a usare questo emulatore.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Come usare Android Device Manager per creare e personalizzare dispositivi virtuali.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md)

Come usare Android Device Manager per modificare le proprietà del profilo di un dispositivo virtuale Android.

### <a name="troubleshooting-emulator-setup-problemsandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Risoluzione dei problemi di configurazione dell'emulatore](~/android/get-started/installation/android-emulator/troubleshooting.md)

Come diagnosticare e correggere i problemi di Android Device Manager che possono verificarsi quando si configura l'emulatore Android.


Dopo aver configurato l'emulatore Android, vedere [Debug con l'emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md) per informazioni su come avviare l'emulatore e usarlo per il test e il debug dell'app.


> [!NOTE]
> A partire da Android SDK Tools **26.0.1** e versioni successive, Google ha rimosso il supporto per le funzionalità AVD/SDK Manager esistenti a favore dell'uso dei nuovi strumenti dell'interfaccia della riga di comando. A causa di queste funzionalità deprecate, vengono ora usati SDK Manager/Device Manager di Xamarin anziché SDK Manager/Device Manager di Google per Android Tools 26.0.1 e versioni successive. Per altre informazioni su Xamarin SDK Manager, vedere [Configurazione di Android SDK](~/android/get-started/installation/android-sdk.md).

