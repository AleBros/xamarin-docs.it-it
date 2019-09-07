---
title: Configurazione dell'emulatore Android
description: È possibile eseguire l'emulatore Android in un'ampia gamma di configurazioni per simulare i diversi dispositivi. Questa guida descrive come preparare l'emulatore Android per testare l'app.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: cb8dc64a8c40496aaf2ea23a834effb58436618a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759083"
---
# <a name="android-emulator-setup"></a>Configurazione dell'emulatore Android

_Questa guida descrive come preparare l'emulatore Android per testare l'app._

## <a name="overview"></a>Panoramica

È possibile eseguire l'emulatore Android in un'ampia gamma di configurazioni per simulare i diversi dispositivi. Ogni configurazione è chiamata _dispositivo virtuale_. Quando si distribuisce e si testa l'app nell'emulatore, si seleziona un dispositivo virtuale preconfigurato o personalizzato che simula un dispositivo Android fisico, come un telefono Pixel o Nexus.

Le sezioni elencate di seguito descrivono come accelerare l'emulatore Android per ottenere prestazioni ottimali, come usare Android Device Manager per creare e personalizzare i dispositivi virtuali e come personalizzare le proprietà del profilo di un dispositivo virtuale. Inoltre, una sezione sulla risoluzione dei problemi illustra i problemi dell'emulatore più comuni e le relative soluzioni.

## <a name="sections"></a>Sezioni

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Accelerazione hardware per le prestazioni dell'emulatore](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Come preparare il computer per ottenere prestazioni ottimali dell'emulatore Android usando la tecnologia di virtualizzazione Hyper-V o HAXM. Poiché l'emulatore Android può risultare estremamente lento senza l'accelerazione hardware, è consigliabile abilitare l'accelerazione hardware nel computer prima di iniziare a usare l'emulatore.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Come usare Android Device Manager per creare e personalizzare dispositivi virtuali.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Modifica delle proprietà del dispositivo virtuale Android](~/android/get-started/installation/android-emulator/device-properties.md)

Come usare Android Device Manager per modificare le proprietà del profilo di un dispositivo virtuale.

### <a name="android-emulator-troubleshootingandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Risoluzione dei problemi dell'emulatore Android](~/android/get-started/installation/android-emulator/troubleshooting.md)

In questo articolo sono descritti i problemi e i messaggi di avviso più comuni che si verificano durante l'esecuzione dell'emulatore Android, oltre a suggerimenti e soluzioni alternative.

Dopo aver configurato l'emulatore Android, vedere [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md) per informazioni su come avviare l'emulatore e usarlo per il test e il debug dell'app.

> [!NOTE]
> A partire da Android SDK Tools **26.0.1** e versioni successive, Google ha rimosso il supporto per le funzionalità AVD/SDK Manager esistenti a favore dell'uso dei nuovi strumenti dell'interfaccia della riga di comando. A causa di queste funzionalità deprecate, vengono ora usati SDK Manager/Device Manager di Xamarin anziché SDK Manager/Device Manager di Google per Android Tools 26.0.1 e versioni successive. Per altre informazioni su Xamarin SDK Manager, vedere [Configurazione di Android SDK per Xamarin.Android](~/android/get-started/installation/android-sdk.md).
